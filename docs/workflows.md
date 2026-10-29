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
   ├── Loop through each section in outline
   ├── Supply section title + target section word count
   ├── Inject 1-sentence summary of previous sections (Context Memory)
   └── Store completed section HTML in payload
           │
           ▼
[ Stage 3: Local PHP Assembly ]
   ├── Combine sections natively in PHP
   ├── Ensure H2/H3 heading hierarchy
   ├── Convert to valid Gutenberg blocks
   └── Inject contextual internal links into paragraphs
           │
           ▼
[ Stage 4: SEO & Quality Control ]
   ├── Generate SEO title, description, and focus keyword
   ├── Generate 3-question FAQ JSON-LD schema
   └── Validate word count tolerance & heading validity
           │
           ▼
[ Stage 5: WordPress Persistence ]
   ├── Insert post into `wp_posts` (Default: `draft`)
   ├── Save meta to Yoast / Rank Math / AIOSEO or native meta
   ├── Save revision snapshot in `wp_cta_ce_history`
   └── Increment monthly generation counter (if on Free tier)
```

---

## 2. Automated Scheduled Publishing Workflow (Pro)

Enables hands-free, recurring publication on a pre-defined schedule:

```
[ Admin Schedule Definition ]
   ├── Days of week (e.g., Mon, Wed, Fri)
   ├── Times of day (e.g., 10:00, 16:00)
   ├── Target WordPress Category
   └── Target Word Count & Language
           │
           ▼
[ Minutely WP-Cron Worker (`cta_ce_minutely_event`) ]
   │
   ├── Acquire 5-Minute Transient Lock (`cta_ce_cron_running`)
   │      (Prevents concurrent worker overlap)
   │
   ├── Verify License Permission (`CTA_CE_License::can('automation')`)
   │
   ├── Check Recurring Schedules (`wp_cta_ce_schedules`)
   │      └── If current time matches slot & not previously run:
   │             └── Insert new task into `wp_cta_ce_queue` (Status: `pending`)
   │
   ├── Fetch Pending Queue Batch (Default: 2 items per run)
   │
   ├── Execute Generation with Provider Lock
   │      └── Increment attempts counter (`attempts + 1`)
   │
   ├── Insert Completed Post as Draft / Published
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

## 5. Commercial License Transfer Workflow

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
