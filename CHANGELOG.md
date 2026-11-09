# Changelog — Content Engine Pro

All notable changes to **Content Engine Pro** are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

Official Product Page: [https://code-elta6ur.net/shop/content-engine-pro](https://code-elta6ur.net/shop/content-engine-pro)

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

