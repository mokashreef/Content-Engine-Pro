# Publishing Workflows — Content Engine Pro

This document maps the end-to-end architectural workflows supported by **Content Engine Pro**, illustrating inputs, processing stages, state management, and output artifacts.

---

## 1. On-Demand Single Article Workflow

Used for generating individual, high-depth articles on demand through the admin interface:

```
[ User Input ]
   ├── Topic & Research Notes
   ├── Target Word Count (e.g., 2,500 words)
   ├── Tone & Target Audience
   └── Language (Arabic or English)
           │
           ▼
[ Stage 1: Outline Generation ]
   ├── Calculate section count (7 sections for 2,500w)
   ├── Query AI provider with structured JSON instruction
   └── Store outline in session / queue payload
           │
           ▼
[ Stage 2: Sequential Section Generation ]
   ├── Loop through each section in outline (1.5s–2.0s LLM rate pacing)
   ├── Supply section title + target section word count
   ├── Pass lightweight `{ title, summary }` context (avoids WAF/`max_input_vars` drops)
   ├── Auto-retry HTTP 429 / timeouts on frontend (`postAjaxWithRetry`, 3s backoff)
   └── Store completed section Gutenberg blocks in payload
           │
           ▼
[ Stage 3: Local PHP Assembly & Link Safety ]
   ├── Combine sections natively in PHP (0 AI tokens consumed)
   ├── Normalize H2/H3 hierarchy and wrap loose HTML in native Gutenberg blocks
   ├── Protect `<pre>`, `<code>`, `<a target="_blank">` via placeholder masking
   └── Inject contextual internal links safely outside attributes and `<code>`
           │
           ▼
[ Stage 4: SEO & Quality Control ]
   ├── Generate SEO title, description, and focus keyword
   ├── Generate 3-question FAQ JSON-LD schema
   └── Validate word count tolerance & heading validity
           │
           ▼
[ Stage 5: Safe Gutenberg WordPress Persistence ]
   ├── Validate & mask `<!-- wp:... -->` delimiters (`CTA_CE_Security::sanitize_gutenberg_content`)
   ├── Sanitize inner HTML via `wp_kses_post()` and insert post via `insert_gutenberg_post()`
   ├── Save meta to Yoast / Rank Math / AIOSEO or native meta
   ├── Save revision snapshot in `wp_cta_ce_history`
   └── Increment monthly generation counter (if on Free tier)
```

---

## 2. Automated Scheduled Publishing Workflow (Pro)

Enables hands-free, recurring publication on a pre-defined schedule:

```
[ Admin Schedule Definition (`wp_cta_ce_schedules`) ]
   ├── Days of week (e.g., Sat–Fri checkboxes)
   ├── Flexible Publishing Times (normalized via `normalize_time_slot()`, e.g., `9:00`, `10:00 AM`, `18:00`)
   ├── Sequential Topic Bank (`topics` textarea, one article title per line)
   ├── Target WordPress Category (fallback contextual topic if bank empty)
   ├── Calculated Next Run (`next_run` stored in UTC, rendered in WP timezone)
   └── Target Word Count & Language
           │
           ▼
[ Minutely WP-Cron Worker (`cta_ce_minutely_event` with `init` Self-Healing) ]
   │
   ├── Acquire 2-Minute Transient Lock (`cta_ce_cron_running`)
   │      (Prevents concurrent worker overlap without prolonged deadlocks)
   │
   ├── Auto-Recover Stale `processing` Jobs (`recover_stale_processing_items(4)`)
   │      (Rescues crashed shared-host jobs > 4 minutes old back to `retry`)
   │
   ├── Check Recurring Schedules (`CTA_CE_License::can('automation')`)
   │      └── If current local slot is due (6h lookback) & `last_run < slot_ts`:
   │             ├── Pop top topic from `topics` bank (or synthesize category topic)
   │             ├── Persist remaining `topics` + recalculate `next_run`
   │             └── Enqueue new `single` task into `wp_cta_ce_queue`
   │
   ├── Fetch Pending Queue Batch (with 40s Shared-Host Time Budget)
   │
   ├── Execute Generation with Provider Lock & Incremental Section Checkpoints
   │
   ├── Insert Completed Post via `CTA_CE_Security::insert_gutenberg_post()`
   │      (Assigns admin author & preserves `<!-- wp:... -->` blocks in Cron)
   │
   ├── Update Task Status (`completed`)
   │
   └── Release Transient Lock
```

---

## 3. Educational Course Curriculum Workflow (Pro)

Structures and schedules a cohesive, multi-lesson course:

```
[ Course Subject Input ]
   └── Topic (e.g., "Full-Stack React & Node.js") + Lesson Count (e.g., 6)
           │
           ▼
[ Syllabus Synthesis by AI ]
   └── Generates structured syllabus with progressive lesson titles
           │
           ▼
[ Curriculum Editor (Admin UI) ]
   ├── Edit, reorder, or delete lessons
   └── Add specific editorial notes per lesson
           │
           ▼
[ Course Approval & Enqueueing ]
   ├── Register course in `cat_series` custom post type
   └── Store lesson syllabus in `_cta_ce_series_lessons`
           │
           ▼
[ Progressive Lesson Generation ]
   ├── For Lesson #3:
   │      Prompt receives:
   │      - Course Title
   │      - Current Lesson Index (3/6)
   │      - Summaries of Lessons #1 and #2
   │      - Scope limitation: Do not repeat concepts taught in Lessons #1 and #2
   │
   └── Generated lesson is saved in `wp_posts` with:
          - `_cta_ce_series_id` = Series Post ID
          - `_cta_ce_lesson_index` = 3
```

---

## 4. Resilience & Exponential Backoff Retry Workflow

Protects the publishing pipeline against network dropouts and rate limits:

```
[ Active Job Execution ]
           │
           ▼
[ API Call Failure (e.g., HTTP 429 or Timeout) ]
           │
           ├── Has this job exceeded Max Attempts (3)?
           │      │
           │      ├── YES:
           │      │     ├── Update status to `failed`
           │      │     └── Write critical error to `wp_cta_ce_logs`
           │      │
           │      └── NO:
           │            ├── Preserve all completed sections in `payload`
           │            ├── Increment attempts count
           │            ├── Calculate retry delay (Attempt 1: 5m, Attempt 2: 15m, Attempt 3: 30m)
           │            ├── Set `scheduled_at` = `now() + delay`
           │            └── Update status to `retry`
           │
           ▼
[ Next Cron Interval (after delay) ]
   └── Resumes execution from the exact section that failed
```

---

## 5. Shared-Host Time-Budget Checkpointing & Stale Recovery Workflow (v2.0.2+)

Shared WordPress hosting environments (such as LiteSpeed / Hostinger) often enforce 60–120 second PHP execution ceilings. Content Engine Pro prevents mid-article process kills and frozen `"processing"` jobs through two coordinated mechanisms:

```
[ Section Loop Inside `CTA_CE_Queue_Manager::process_article_generation()` ]
           │
           ├── Save each completed section immediately into `payload['completed_sections']`
           │
           ├── Before starting next uncompleted section:
           │      └── Check elapsed runtime: `(microtime(true) - $start_time) >= 40s`?
           │             │
           │             ├── YES (and >= 1 section completed):
           │             │     ├── Keep `completed_sections` persisted in DB
           │             │     ├── Reset `status = 'pending'`, restore `attempts` (zero penalty)
           │             │     ├── Schedule `wp_schedule_single_event(time() + 3, 'cta_ce_minutely_event')`
           │             │     └── Return `continue: true` to Queue Manager AJAX (auto-chains next step)
           │             │
           │             └── NO: Generate next section normally (with 2.0s rate pacing)
           │
[ Fallback Safety Net: If Host Kills PHP Unexpectedly Mid-Request ]
           │
           ▼
[ Next Queue Load / Cron Tick / Batch Trigger ]
   └── Calls `CTA_CE_DB::recover_stale_processing_items(4)`
          ├── Finds `status = 'processing'` with `updated_at <= UTC_NOW - 4 minutes`
          ├── Preserves all saved `completed_sections`
          └── Moves `attempts < max` -> `retry` (or `failed` if exhausted)
```

---

## 6. Commercial License Transfer Workflow

Permits customers to move their single-site commercial license between domains:

1. **On Origin Site (`site-a.com`)**:
   * Navigate to **Content Engine** -> **License**.
   * Click **Deactivate License**.
   * Plugin sends a signed deactivation request to `https://code-elta6ur.net/api/v1/license/deactivate`.
   * The licensing server frees the domain slot.
   * Local origin site reverts safely to the Free Plan without deleting any posts or data.
2. **On Destination Site (`site-b.com`)**:
   * Install Content Engine Pro.
   * Navigate to **Content Engine** -> **License**.
   * Enter the same commercial license key and click **Activate License**.
   * The licensing server binds the key to `site-b.com`, unlocking full Pro automations immediately.

