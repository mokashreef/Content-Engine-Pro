# Troubleshooting Guide — Content Engine Pro

This guide outlines common diagnostic scenarios, error codes, and safe resolutions for **Content Engine Pro**.

---

## 1. Commercial Licensing Errors

| Error Message / Status | Root Cause | Resolution |
| :--- | :--- | :--- |
| **`STATUS_INVALID`**<br>*"مفتاح الترخيص غير مسجل أو غير صالح"* | The entered key does not exist on the licensing server or was mistyped. | Verify that the key matches the standard uppercase format: `CEPRO-XXXX-XXXX-XXXX-XXXX`. Check your order confirmation email from [Code Elta6ur](https://code-elta6ur.net/). |
| **`STATUS_LIMIT_REACHED`**<br>*"تم تجاوز الحد الأقصى للمواقع المسموح بها"* | The single-site license is already bound to another active domain. | Log into your previous WordPress site and click **Deactivate License** in **Content Engine** -> **License**. If the old site is inaccessible, contact [support@code-elta6ur.net](mailto:support@code-elta6ur.net) for a manual slot reset. |
| **`STATUS_EXPIRED`**<br>*"انتهت صلاحية هذا الترخيص السنوي"* | The 1-year commercial subscription has lapsed. | Visit [https://code-elta6ur.net/shop/content-engine-pro](https://code-elta6ur.net/shop/content-engine-pro) to renew your subscription. Existing posts and data remain completely safe. |
| **`STATUS_CONNECTION_ERROR`**<br>*"تعذر الاتصال بخادم التراخيص"* | Web server cannot establish an outbound HTTPS connection to `https://code-elta6ur.net`. | Check your server's outbound firewall rules (Port 443). Active licenses automatically enter a **14-Day Grace Period**, keeping all automations running. |
| **`tamper_detected`**<br>*"تم رصد تعديل غير مصرح به"* | The `cta_ce_license_data` row in `wp_options` was manually edited, invalidating the HMAC-SHA256 signature. | Re-enter your legitimate license key in the admin screen and click **Activate License** to compute a fresh valid signature. |

---

## 2. AI Provider & Generation Failures

### Symptom: HTTP 429 (Rate Limit Exceeded)
* **Cause**: Your configured provider's per-minute (RPM) or daily (RPD) request limit was reached (common on free-tier Gemini or Groq).
* **Automatic Handling**: Content Engine Pro automatically switches to your next prioritized provider.
* **Resolution**:
  1. In **Content Engine** -> **Providers (AI)**, configure at least two providers (e.g., Gemini and Groq) to ensure fallback redundancy.
  2. Lower the generation batch frequency if running high-volume recurring schedules.

---

### Symptom: API Request Timed Out (cURL Error 28)
* **Cause**: The AI provider endpoint took longer to respond than the configured timeout limit, or your hosting server has an aggressive execution limit.
* **Resolution**:
  1. Go to **Content Engine** -> **Providers (AI)**.
  2. Increase the **Timeout** setting for that provider (e.g., from 60 seconds to 120 seconds).
  3. In `wp-config.php`, ensure PHP `max_execution_time` is set to at least 120 seconds.

---

### Symptom: Free Tier Cap Reached (`FREE_LIMIT_REACHED`)
* **Message**: *"لقد وصلت إلى الحد الأقصى للخطة المجانية (6/6 مقالات لهذا الشهر)."*
* **Cause**: You have successfully generated 6 articles this calendar month on the Free Plan.
* **Resolution**:
  * Upgrade to [Content Engine Pro](https://code-elta6ur.net/shop/content-engine-pro) for unlimited generations, or wait until the 1st day of the next calendar month when the counter resets.

---

### Symptom: Word Count Exceeded on Free Plan (`FREE_WORD_LIMIT_EXCEEDED`)
* **Message**: *"الحد الأقصى لعدد الكلمات في الخطة المجانية هو 700 كلمة لكل مقال."*
* **Cause**: Requested word count exceeds 700 words without an active Pro license.
* **Resolution**: Set target word count to <= 700 words, or activate a Pro license key to unlock the 6,000-word chunker.

---

## 3. Scheduled Tasks & WP-Cron Issues

### Symptom: Scheduled Articles Are Not Generating Automatically
* **Cause 1**: Default WordPress WP-Cron relies on site visitors to trigger execution. Low-traffic sites may not fire cron events on schedule.
* **Cause 2**: Page caching plugins (e.g., WP Rocket, LiteSpeed, Cloudflare) may cache or block requests to `/wp-cron.php`.
* **Diagnostic Steps**:
  1. Check **Content Engine** -> **Operation Logs** for recent `cta_ce_minutely_event` executions.
  2. Check **Content Engine** -> **Task Queue** to see if tasks are stuck in `pending` or `retry`.
* **Resolution**: Configure a real server-side system cron job:
  ```bash
  * * * * * wget -q -O - https://example.com/wp-cron.php?doing_wp_cron >/dev/null 2>&1
  ```

---

### Symptom: Task Stuck in "Processing" Status
* **Cause**: A PHP process crashed or timed out before updating the database row.
* **Resolution**:
  * The queue manager automatically releases concurrency locks after 5 minutes.
  * You can manually click **Run Now** or **Retry** next to any job in **Content Engine** -> **Task Queue**.

---

## 4. Internal Linking & SEO Issues

### Symptom: Generated Articles Have No Internal Links
* **Cause 1**: The site has fewer than 2 published posts. The candidate scanner (`CTA_CE_Internal_Links::get_candidates`) requires published posts to find link targets.
* **Cause 2**: `auto_internal_linking` is disabled in **Content Engine** -> **Settings**.
* **Cause 3**: Generated paragraphs did not contain natural keyword matches corresponding to existing post titles or tags.

---

### Symptom: FAQ Schema Does Not Appear on Frontend
* **Cause 1**: An external SEO plugin (e.g., Yoast or Rank Math) is overriding schema output.
* **Resolution**: Verify that the generated post has FAQ data in the post editor. When a third-party SEO plugin is active, Content Engine Pro populates their respective custom fields. When no SEO plugin is active, it outputs JSON-LD directly into `wp_head`.
