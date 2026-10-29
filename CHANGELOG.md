# Changelog — Content Engine Pro

All notable changes to **Content Engine Pro** are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

Official Product Page: [https://code-elta6ur.net/shop/content-engine-pro](https://code-elta6ur.net/shop/content-engine-pro)

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

