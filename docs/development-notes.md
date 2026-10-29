# Developer & Architectural Notes — Content Engine Pro

This document outlines the core architectural patterns, testing infrastructure, and coding standards used in **Content Engine Pro**.

---

## 1. Architectural Design Patterns

Content Engine Pro is designed with high cohesion and loose coupling across modular services:

### A. Singleton Lifecycle Managers
* **`CTA_CE_Core`**: Central bootstrap and hook orchestrator. Initializes dependency injection, registers activation/deactivation hooks, and listens to `plugins_loaded`.
* **`CTA_CE_Provider_Manager`**: Registry for all AI provider classes. Manages provider priority ordering, provider locks per job, and automated 429 failover.
* **`CTA_CE_License`**: Encapsulates commercial status checks, offline grace periods, monthly free usage limits, and HMAC-SHA256 anti-tamper signing.

### B. Functional Services & Separation of Concerns
* **`CTA_CE_Chunker`**: High-level orchestrator for outline calculation, prompt synthesis, and sequential section writing.
* **`CTA_CE_Block_Converter`**: Pure functional converter transforming raw Markdown/HTML into compliant WordPress Gutenberg block markup.
* **`CTA_CE_Internal_Links`**: Contextual internal linking engine scanning published posts and injecting keyword anchors into paragraph nodes.
* **`CTA_CE_Quality_Check`**: Validates content readability, heading hierarchy, and word count tolerances before persistence.
* **`CTA_CE_SEO`**: Interoperability bridge mapping SEO titles, descriptions, keywords, and JSON-LD FAQ schemas across Yoast SEO, Rank Math, All in One SEO, or native fallback output.
* **`CTA_CE_DB`**: Low-level database abstraction layer managing SQL queries, pagination, and log pruning using `$wpdb->prepare()`.

---

## 2. Automated QA Test Harness

Content Engine Pro includes a comprehensive internal test harness located in the `tests/` directory:

```bash
# Execute the full automated test harness locally:
php tests/run-all-tests.php
```

### Verified Test Suites:
1. **Suite 01: Installation, DB Schema, Migration & Clean Uninstall** (19 checks)
2. **Suite 02: AI Providers, HTTP Status Failures & Auto-Failover** (38 checks)
3. **Suite 03: Content Generation Pipeline, Multilingual & SEO** (48 checks)
4. **Suite 04: Queue Scaling, Retries, Backoff & Concurrency Locking** (20 checks)
5. **Suite 05: Cron Scheduling, Timezones, Bounds & Deduplication** (24 checks)
6. **Suite 06: Security, Permissions, XSS/SQLi & Input Hardening** (54 checks)
7. **Suite 07: UX, Responsive CSS, View Rendering & Production Scan** (67 checks)
8. **Suite 08: Commercial Licensing, Gating, Anti-Tamper & Grace** (58 checks)

**Total Test Coverage: 328 empirically verified checks (0 failures).**

---

## 3. WordPress Coding Standards & Security

* **Prefixing**: All classes use `CTA_CE_`, functions use `cta_ce_`, constants use `CTA_CE_`, and database tables use `{$wpdb->prefix}cta_ce_`.
* **Direct Access Defense**: Every PHP file verifies `defined('WPINC') || defined('ABSPATH')` or terminates execution.
* **Database Queries**: Raw SQL queries are prohibited. Every dynamic query must pass through `$wpdb->prepare()`.
* **AJAX & Nonce Handling**: AJAX endpoints verify nonce validity and user capabilities via `CTA_CE_Security::verify_admin()`.
