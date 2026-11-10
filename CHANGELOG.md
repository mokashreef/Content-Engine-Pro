# Changelog — Content Engine Pro

All notable changes to **Content Engine Pro** are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

Official Product Page: [https://code-elta6ur.net/shop/content-engine-pro](https://code-elta6ur.net/shop/content-engine-pro)

---

## [2.0.4] — 2026-11-10

### Native WordPress Gutenberg Block Preservation Fix
This update resolves the issue where WordPress stripped `<!-- wp:... -->` Gutenberg block comments during post insertion (`wp_kses_post` and WP-Cron `content_save_pre` filters), causing generated articles to appear in the WordPress editor as raw classic HTML instead of native Gutenberg blocks.

### Fixed
* **Gutenberg Block Comment Stripping on Save (`CTA_CE_Security::sanitize_gutenberg_content` & `insert_gutenberg_post`)**:
  * WordPress Core's `wp_kses_post()` and unauthenticated WP-Cron `content_save_pre` KSES filters strip all HTML comments, deleting every `<!-- wp:... -->` and `<!-- /wp:... -->` block delimiter before storing `post_content`.
  * Implemented `sanitize_gutenberg_content()` to validate Gutenberg block markers and JSON attributes, mask them, run `wp_kses_post()` on inner HTML, and restore block markers cleanly.
  * Implemented `insert_gutenberg_post()` to assign valid admin authorship during WP-Cron runs and preserve validated Gutenberg blocks through `wp_insert_post()` / `wp_update_post()` across manual generation, queue processing, and history restoration.
* **Mixed-Block Wrapping (`CTA_CE_Block_Converter::normalize_existing_blocks`)**: Enhanced block normalizer with a block-depth scanner that converts any stray raw HTML fragments outside `<!-- wp:... -->` blocks into official Gutenberg blocks (`wp:paragraph`, `wp:list`, `wp:heading`, etc.), ensuring 100% block coverage across entire posts.
* **Test Harness Parity**: Updated mock `wp_kses_post()` in `tests/bootstrap.php` to strip HTML comments just like WordPress Core and added 4 empirical test assertions verifying Gutenberg block survival in saved posts.

---

## [2.0.3] — 2026-11-10

### HTML Link & Code Formatting Protection Fix
This release fixes a critical HTML corruption bug where multiple `target="_blank"` hyperlinks on the same line were broken into raw text by Markdown underscore parsing.

### Fixed
* **Multiple `target="_blank"` Link Corruption (`CTA_CE_Block_Converter`)**: Fixed a regex conflict in `clean_artifacts()` where Markdown italic conversion (`_..._`) matched across the underscores of consecutive `target="_blank"` attributes on the same line (producing invalid `target="<em>blank"` tags that broke WordPress `wp_targeted_link_rel()` and `wp_kses_post()`). All `<pre>`, `<code>`, HTML comments, existing HTML tags/attributes, converted Markdown links, and bare URLs are now protected via deterministic placeholder masking prior to inline Markdown transformation, and underscore italics enforce strict Unicode word boundaries.
* **DOM Serialization & Entity Preservation**: Removed destructive `html_entity_decode()` in `get_inner_html()` and `get_outer_html()`, keeping UTF-8 Arabic characters, `&amp;` query parameters, and `&lt;...&gt;` code snippets intact without unescaping into live HTML.
* **Trailing Period Placement & Unclosed `<a>` Balancing**: Added `'a'` to `close_open_html_tags()` and guarded `repair_trailing_sentence()` against appending stray periods outside closing Gutenberg block comments (`<!-- /wp:... -->`).
* **Internal Linking Tag Guard (`CTA_CE_Internal_Links::inject_links`)**: Masked `<code ...>...</code>` and HTML tag attributes (e.g., `alt`, `title`) inside paragraphs prior to phrase matching so internal links are never injected inside existing attributes.

---

## [2.0.2] — 2026-11-10

### Stale Processing Recovery, Checkpoint Continuation & Queue UI/Provider Hardening
This update resolves frozen `"قيد المعالجة"` (Processing) queue jobs caused by shared-hosting execution limits, introduces step-by-step checkpoint continuation, improves Gemini compatibility, and fixes Queue Manager table/UI layout bugs.

### Added
* **Automatic Stale Processing Recovery (`CTA_CE_DB::recover_stale_processing_items`)**: Detects queue jobs stuck in `processing` status for over 4 minutes due to server/LiteSpeed timeouts or crashes, automatically recovering them to `retry` (or `failed` if max attempts reached) while preserving all previously saved `completed_sections`.
* **Time-Budgeted Checkpoint Continuation**: Background cron and Queue Manager AJAX actions now enforce a 40-second PHP execution budget between sections on live servers. Long multi-section articles pause gracefully after saving completed sections and resume seamlessly via automatic frontend AJAX chaining or immediate follow-up cron events without burning retry attempts.
* **Manual Resume Button for Processing Rows**: Added an explicit **Resume ("استئناف")** action button directly on `processing` items in the Queue Manager so administrators can rescue any stalled task in one click.
* **Expandable Full Error Inspection & Section Progress Badges**: Queue rows now display section generation progress (`X / Y`) and an expandable full error viewer (`التفاصيل`) instead of truncating long API diagnostic errors.

### Fixed
* **Queue Manager Table & Filter Bar CSS Layout**:
  * Styled `.cta-ce-filter-tabs`, `.cta-ce-tab-link`, `.cta-ce-action-group`, `.cta-ce-info-alert`, `.cta-ce-btn-secondary`, dashboard timeline/feature lists, and audit log filter controls.
  * Removed destructive global `word-break: break-word` inside `.cta-ce-table td` and applied `.cta-ce-table-wrapper` + `.cta-ce-nowrap` across all admin tables (`queue`, `schedules`, `articles`, `logs`, `usage`, `series`, `dashboard`), fixing cramped character-by-character wrapping on IDs, provider names, dates, and status badges.
  * Fixed CSS selector collision between `.cta-ce-stat-icon.cta-ce-stat-info` and `.cta-ce-stat-info` containers.
  * Fixed mismatched container closing tags in `articles.php` and `dashboard.php` and incorrect field focus ID in `series.php`.
* **Gemini Model Parsing & API Timeout Resiliency**:
  * Updated Google Gemini provider to skip internal `thought` response parts on thinking models, report `finishReason` / `blockReason` diagnostics when responses are empty, and set `gemini-2.0-flash` as default fallback model.
  * Standardized default API timeout to 90 seconds across cloud providers (`gemini`, `groq`, `openrouter`, `mistral`, `cerebras`), increased chunk pacing to 2.0s and single-provider HTTP 429 backoff to 6s on live servers, and cast `completed_sections` indexes strictly to `int` to eliminate duplicate section runs.
* **Version Header & Asset Cache Busting**: Synchronized plugin header version and asset version constant (`2.0.2`) with `readme.txt` to clear browser and optimization plugin (e.g., 10Web Booster) asset caches cleanly.

---

## [2.0.1] — 2026-11-09

### Maintenance, Stability & Automation Fixes
This patch release resolves critical production workflow issues in the automated queue scheduler, sequential long-form generator, and recurring schedule topic management.

### Added
* **Schedule Topic Bank**: Added `topics` column in `wp_cta_ce_schedules` table with automatic database migration. Users can now define an ordered bank of specific article titles and topics (one per line) for recurring rules. The engine pops topics sequentially on each scheduled run.
* **On-Demand Queue Batch Execution ("Run Batch Now")**: Added a header action button in the Queue Manager and corresponding AJAX handler (`cta_ce_run_queue_batch`) allowing administrators to process pending queue items immediately without waiting for WP-Cron.
* **Direct Add to Queue from Generator**: Added "Add to Queue" (`cta_ce_queue_single_article`) button in the manual generator view so users can queue articles for background generation without running them synchronously in the browser.
* **WP-Cron Auto-Healing**: Added an initialization check in `CTA_CE_Core::init()` that verifies `cta_ce_minutely_event` on every admin request and automatically reschedules it if missing from WordPress cron.
* **Real-Time Queue Item Execution**: Added immediate execution mode (`run_now: 1`) when clicking "Retry" or "Start Now" on queue items, complete with an interactive loading spinner and real-time user notification upon completion.

### Fixed
* **Queue Item Premature Failure ("Tried and Failed")**: Fixed a critical bug in `CTA_CE_DB::retry_queue_item()` where retried items retained their previous attempt counter (`attempts = 3`). Retries now reset `attempts = 0` and clear previous error messages so items receive a fresh execution cycle.
* **Unresponsive Queue Action Buttons**: Fixed queue action handler so clicking "Restart" or "Start Now" actively triggers item execution via AJAX instead of merely resetting status to pending.
* **Generic "Scheduled Article" Titles**: Fixed `CTA_CE_Cron` generating dummy placeholder titles (`"مقال مجدول تلقائياً: ..."`); the scheduler now draws real topics from the rule's topic bank, or automatically synthesizes a high-value title based on the assigned category.
* **Generator Disconnecting Midway**: Fixed browser connection drops and HTTP 500/WAF errors during sequential multi-section generation by:
  * Stripping redundant HTML from `prior_sections` payload, passing only lightweight `{ title, summary }` objects (95% payload reduction).
  * Adding a 1.5-second pacing delay between section generation requests to respect LLM rate limits (TPM/RPM).
  * Implementing `postAjaxWithRetry` in `admin-generator.js` with automatic 3-second backoff retry on HTTP 429 rate limit errors.
  * Raising PHP script execution time limits to 180 seconds for section generation and assembly.
* **Schedule Time Parsing & Formatting Inaccuracies**: Fixed `CTA_CE_Cron` silently failing on single-digit or non-padded time strings (e.g., `9:00`, `10:00 AM`, `2:30 pm`) via a new `normalize_time_slot()` utility.
* **Accurate Next Run Calculation**: Added automatic calculation and storage of `next_run` UTC timestamps upon schedule creation and after each execution, displaying upcoming run dates in the schedules table.
* **Concurrency Lock Stalling**: Reduced cron transient lock duration from 10 minutes to 2 minutes (`MINUTE_IN_SECONDS * 2`) to prevent prolonged queue lockouts after unexpected server disruptions.
* **Admin Script Localization**: Localized `ctaCeData` across all admin scripts (`cta-ce-calendar-js`, `cta-ce-providers-js`, `cta-ce-queue-js`, `cta-ce-series-js`) ensuring reliable access to nonces and AJAX URLs on all plugin screens.

---

## [2.0.0] — 2026-09-30

### Commercial Production Release
This release establishes the commercial production architecture of Content Engine Pro under Code Elta6ur.

### Added
* **Commercial Licensing Subsystem**: Full integration with the Code Elta6ur authoritative licensing server with HMAC-SHA256 request signing and local anti-tampering protection.
* **14-Day Offline Grace Period**: Resilience safeguard allowing uninterrupted Pro automations during temporary licensing server downtime or host network dropouts.
* **Database Migration Engine**: Schema migration runner (`CTA_CE_Activator::maybe_upgrade`) supporting seamless upgrades without data loss.
* **Automated Log Rotation**: Daily background task purging log entries older than 30 days from `wp_cta_ce_logs`.
* **About & System Diagnostics**: Dedicated admin view (`cta-ce-about`) displaying server runtime metrics, memory allocation, execution timeouts, and WP-Cron health.
* **Automated QA Test Harness**: 8 comprehensive automated test suites covering 328 empirically verified assertions with zero failures.

### Changed
* **Enhanced Privilege Checks**: Elevated all configuration, licensing, import/export, and log clearance handlers to require strict `manage_options` capability.
* **Calendar Date Arithmetic**: Improved monthly publishing calendar calculations to dynamically handle month boundaries (28 to 31 days).
* **Internal Linking Engine**: Refined contextual paragraph candidate matching and Unicode boundary detection.
* **Asset Loading Optimization**: Admin scripts and styles now enqueue exclusively on plugin-specific pages (`cta-ce-*`), preventing script bloat on third-party WordPress admin screens.

### Security
* **FAQ Schema XSS Hardening**: Escaped structured JSON-LD scripts using `JSON_HEX_TAG | JSON_HEX_AMP` and `wp_kses_post()` to eliminate script injection vectors.
* **Secret Redaction**: Automatic scrubbing of Bearer tokens, API credentials, and authorization query parameters before persisting entries to database logs.

---

## [1.0.1] — 2026-09-14

### Changed
* Enhanced generation stability and HTTP error handling for external provider endpoints.
* Improved retry delays and queue status transitions.

---

## [1.0.0] — 2026-09-01

### Added
* Initial core release of the content orchestration engine.
* Multi-provider support for Google Gemini, Groq Cloud, OpenRouter, Mistral AI, Cerebras Cloud, and local Ollama.
* Multi-part sequential chunker and local PHP Gutenberg block assembly.
* Custom post type `cat_series` for educational curriculum series.
* Background task queue and minutely WP-Cron scheduler.

---

## Release Verification Checklist

Before publishing any future release, the following checklist must be completed:

- [ ] **Version Consistency**: Version string matches across `code-elta6ur-content-engine.php`, `readme.txt`, `CTA_CE_VERSION`, and documentation.
- [ ] **Database Versioning**: If schema changes occurred, `CTA_CE_DB_VERSION` is incremented and tested via `maybe_upgrade()`.
- [ ] **Automated Test Harness**: `php tests/run-all-tests.php` completes with 100% passed checks (0 failures).
- [ ] **Secret Scan**: Workspace verified for zero committed secrets or unredacted credentials.
- [ ] **Package Integrity**: Production ZIP built via `bin/package-plugin.php` excludes `.git`, `tests/`, `licensing-server/`, and documentation drafts.
- [ ] **Documentation Sync**: All modified or new features are documented in `README.md` and `docs/`.
- [ ] **Purchase & Download Links**: Official store links point accurately to `https://code-elta6ur.net/shop/content-engine-pro`.

