# Security & Privacy Policy — Content Engine Pro

This document details the security architecture, data governance principles, and external network transmissions implemented in **Content Engine Pro**.

---

## 1. Core Data Governance Principles

* **Zero Content Interception**: Your topics, prompts, generated drafts, and published posts are stored strictly within your local WordPress database (`wp_posts`). They are never uploaded, logged, or analyzed by Code Elta6ur servers.
* **No Middleman AI Proxies**: Network requests to AI providers (Google Gemini, Groq, OpenRouter, Mistral, Cerebras) travel directly from your web server to the vendor's official endpoint via encrypted TLS 1.3.
* **No Telemetry or Tracking Scripts**: Content Engine Pro does not bundle analytics beacons, tracking pixels, Mixpanel, or Google Analytics into your WordPress administration interface.
* **Strict Non-Custodial Key Handling**: Your third-party AI credentials belong entirely to you and reside encrypted in your WordPress options table.

---

## 2. External Network Communications Inventory

Below is an exhaustive, auditable inventory of all outbound network transmissions performed by the plugin:

### A. Communications with Configured AI Providers
* **Destination**: The official endpoint of the provider selected by the user (e.g., `generativelanguage.googleapis.com`, `api.groq.com`).
* **Payload**:
  * Topic prompt, section outline instructions, requested word count, and target language.
  * Your private API key passed in the standard `Authorization: Bearer` or `x-goog-api-key` header.
* **Trigger**: Manual generation, scheduled cron batch, or "Test Connection" button.

### B. Communications with Code Elta6ur Licensing Server
* **Destination**: `https://code-elta6ur.net/api/v1/license/`
* **Payload**:
  * `license_key`: Customer's purchased Pro license key.
  * `domain`: Normalized domain name (e.g., `example.com`).
  * `site_url`: WordPress site URL (`home_url()`).
  * `site_uuid`: Randomly generated, anonymous installation UUID (`cta_ce_install_uuid`).
  * `version`: Installed plugin version string (e.g., `2.0.0`).
* **Authentication**: Signed via HMAC-SHA256 (`X-CTA-Signature`) using the installation UUID as secret.
* **Frequency**:
  * On-demand upon license activation or deactivation.
  * Automated background synchronization throttled to once every 7 days via WP-Cron.

---

## 3. WordPress Security Implementation

Content Engine Pro follows established WordPress security standards:

* **Cross-Site Request Forgery (CSRF) Hardening**: All administrative POST submissions and AJAX handlers execute strict Nonce verification via `CTA_CE_Security::verify_admin()` using the action `cta_ce_admin_nonce_action`.
* **Capability & Privilege Checks**:
  * Administrative controls (settings, provider keys, license management, logs, import/export) strictly require the `manage_options` capability.
  * Article creation is restricted to users with at least `edit_posts` permissions.
* **SQL Injection Prevention**: All queries against custom tables (`wp_cta_ce_queue`, `wp_cta_ce_schedules`, `wp_cta_ce_logs`, `wp_cta_ce_history`) utilize `$wpdb->prepare()` with parameterized type specifiers (`%d`, `%s`).
* **Cross-Site Scripting (XSS) Sanitization**:
  * Admin outputs are escaped using `esc_html()`, `esc_attr()`, and `esc_url()`.
  * Generated article HTML is sanitized via `wp_kses_post()` before database insertion.
  * Structured JSON-LD FAQ schemas are encoded with `JSON_HEX_TAG | JSON_HEX_AMP | JSON_HEX_APOS | JSON_HEX_QUOT` to prevent script breakouts.
* **Automatic Secret Scrubbing in Logs**:
  * The logging service (`CTA_CE_Security::sanitize_log_message`) strips Bearer tokens, API keys, and authorization query strings before writing entries to `wp_cta_ce_logs`.
  * Logs older than 30 days are automatically purged daily to prevent disk and database bloat.
* **Anti-Tampering Verification**:
  * Local license records are signed with an HMAC-SHA256 hash using the WordPress salt `wp_salt('auth')`. Manual database modification of the license record is rejected.

---

## 4. Responsible Vulnerability Disclosure

If you discover a potential security vulnerability in Content Engine Pro, please report it directly and responsibly:

* **Email**: [security@code-elta6ur.net](mailto:security@code-elta6ur.net)
* **Encryption**: PGP keys are available upon request.
* **Scope**: Please include detailed reproduction steps, proof-of-concept code, and your environment specifications (WordPress version, PHP version).
* **Policy**: We acknowledge vulnerability reports within 48 hours and coordinate fixes prior to public disclosure. Please do not report security vulnerabilities in public GitHub issues.
