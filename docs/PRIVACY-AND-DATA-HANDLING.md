# Content Engine Pro — Privacy & Data Handling Statement
**Brand**: `code elta6ur`  
**Product**: Content Engine Pro  
**Developer**: Mohammad Abu Khashreef ([https://mohammad.code-elta6ur.com](https://mohammad.code-elta6ur.com))  
**Last Updated**: September 21, 2026

---

## 1. Transparency Principle

We believe in complete transparency. We do not make deceptive claims like "no data ever leaves your site" when a plugin communicates with AI endpoints or licensing servers. 

Below is an exhaustive, auditable inventory of all data transmissions made by **Content Engine Pro**.

---

## 2. External Network Communications

### A. AI Providers (Google Gemini, Groq, OpenRouter, Mistral, Cerebras)
- **Data Transmitted**:
  - Prompt text containing article topic, section outline, target word count, language (`ar` or `en`), and writing instructions.
  - Customer's own API key in the authorization header.
- **Recipient**: The specific AI vendor selected and configured by the customer.
- **Routing**: Directly from customer's WordPress server to the provider's official API endpoint via HTTPS. Never routed through intermediate proxy servers.

### B. Licensing Server (`https://mohammad.code-elta6ur.com`)
- **Data Transmitted**:
  - `license_key`: Customer's purchased Pro license key.
  - `domain`: Customer's website domain (e.g. `example.com`).
  - `site_url`: Customer's WordPress home URL.
  - `site_uuid`: Randomly generated, anonymous installation identifier.
  - `version`: Installed plugin version (`2.0.0`).
- **Frequency**: On activation, on deactivation, and once every 7 days for background renewal validation.
- **Recipient**: Authoritative licensing verification service.

---

## 3. What is NEVER Transmitted or Collected

1. **No Content Interception**: Your generated articles, drafts, and published content are stored strictly in your local WordPress database (`wp_posts`). They are never uploaded or synced to the licensing server.
2. **No Prompt Telemetry**: The topics and prompts you write are sent only to your chosen AI provider, never to our servers.
3. **No User Analytics / Telemetry**: We do not inject tracking scripts, Google Analytics, Mixpanel, or telemetry beacons into your WordPress admin.
4. **No Customer Credentials**: Database passwords, WordPress admin passwords, and server environment variables are never accessed or transmitted.

---

## 4. Local Data Storage & Sanitization

- **Sensitive Key Masking**: License keys (`CEPRO-****-****-****-1234`) and AI API keys (`AIzaSy...cdef`) are masked in admin views.
- **Secret Scrubbing in Logs**: All HTTP error logs automatically redact Bearer tokens and `api_key=` parameters before inserting into `wp_cta_ce_logs`.
- **Automatic Log Cleanup**: Logs older than 30 days are automatically purged daily to prevent database bloat.
- **Data Retention upon Expiration / Uninstall**:
  - **License Expiration**: All customer data, posts, schedules, and history remain untouched in your database.
  - **Plugin Deactivation**: Customer data is preserved.
  - **Plugin Uninstall**: Customer data is preserved by default. If the administrator explicitly enables "Full Cleanup on Uninstall" in settings, all custom tables and metadata are cleanly dropped.
