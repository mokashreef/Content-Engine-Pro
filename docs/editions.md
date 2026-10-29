# Editions & Licensing Guide — Content Engine Pro

Content Engine Pro offers a transparent two-tier product model: a built-in **Free Plan** for evaluation and light blogging, and a commercial **Pro Plan** ($10 USD / Year) for full-scale autonomous publishing.

Official Purchase Page: [https://code-elta6ur.net/shop/content-engine-pro](https://code-elta6ur.net/shop/content-engine-pro)  
Pricing: **$10 USD / Year** (Single-site license with transferability)

---

## 1. Feature Comparison Matrix

The table below reflects the exact feature authorization checks implemented in the plugin source (`CTA_CE_License` and `CTA_CE_Chunker`):

| Capability / Workflow | Free Plan | Content Engine Pro | Implementation Evidence |
| :--- | :---: | :---: | :--- |
| **Monthly Article Generation Quota** | **6 articles / month** | **Unlimited** | `FREE_MONTHLY_ARTICLE_LIMIT = 6`. Resets monthly based on site timezone. |
| **Maximum Word Count Per Article** | **700 words** | **Up to 6,000 words** | `FREE_MAX_WORD_COUNT = 700`. Pro uses the 12-section chunker engine. |
| **Manual Article Generator** | Included | Included | Generates on-demand articles via active AI provider. |
| **AI Provider Support** | Included | Included | Gemini, Groq, OpenRouter, Mistral, Cerebras, and Ollama/Custom. |
| **Basic On-Page SEO** | Included | Included | Generates SEO title, meta description, and focus keyword. |
| **Long-Form Sequential Chunker** | Limited (Max 700w) | **Included (Up to 6,000w)** | Multi-part outline, sequential chunking, and memory summaries. |
| **Zero-Token Local PHP Assembly** | Included | Included | Compiles Gutenberg blocks natively without using AI token quota. |
| **Automated Internal Linking** | Not Included | **Included** | Gated by `can('advanced_seo')`. Scans published posts and injects links. |
| **Automated JSON-LD FAQ Schema** | Not Included | **Included** | Gated by `can('advanced_seo')`. Outputs structured data with XSS guards. |
| **Minutely WP-Cron Automation** | Not Included | **Included** | Gated by `can('automation')`. Asynchronous background processing. |
| **Recurring Publishing Rules** | Not Included | **Included** | Gated by `can('automation')`. Automated scheduling by days and hours. |
| **Multi-Topic Batch Queue** | Not Included | **Included** | Gated by `can('bulk_generation')`. Persistent queue with retry backoff. |
| **Educational Courses & Series** | Not Included | **Included** | Gated by `can('series_courses')`. `cat_series` CPT with syllabus engine. |
| **Automatic 429 Provider Failover** | Not Included | **Included** | Fallback to backup providers upon rate limit errors. |
| **Revision History & Snapshots** | Included | Included | Version snapshots stored in `wp_cta_ce_history`. |
| **Commercial License Transferability** | N/A | **Included** | One-click deactivation to move license between domains. |
| **Offline Resilience (Grace Period)** | N/A | **14-Day Grace Period** | Uninterrupted automation during temporary licensing server downtime. |

---

## 2. Free Plan Rules & Lifecycle

The Free Plan is active automatically when Content Engine Pro is installed without an active commercial license key:

1. **Monthly Limit Tracking**:
   * Stored in the WordPress option `cta_ce_free_monthly_usage`.
   * Incremented only upon successful article generation (`record_successful_generation`). Failed generations do not penalize your monthly quota.
   * Resets automatically to `0` at the start of each calendar month based on your WordPress installation's timezone setting.
2. **Word Count Enforcement**:
   * Attempting to generate an article exceeding 700 words on the Free Plan returns an informative notice prompting an upgrade to Pro.
3. **Draft Safety**:
   * All articles generated on the Free Plan remain permanently in your WordPress database as standard posts.

---

## 3. Commercial Pro Subscription ($10 USD / Year)

A commercial subscription provides complete access to all autonomous automation, bulk queues, course curriculums, and long-form writing engines.

### Subscription Terms:
* **Cost**: $10 USD per year (equivalent to ~$0.83 per month).
* **Site Allocation**: 1 active WordPress site per license key.
* **Domain Transferability**: If you change domains, redesign your site, or move hosting providers, you can deactivate your license key with one click in the WordPress admin (`Content Engine -> License`) and immediately activate it on your new domain.
* **Safe Renewal**: Annual renewals extend your active license by 365 days without interrupting active cron queues or schedules.

---

## 4. The 14-Day Offline Grace Period

We recognize that commercial sites depend on uninterrupted automation. Content Engine Pro includes an automated **14-Day Grace Period** (`GRACE_PERIOD_DURATION = 1209600`):

* If your web server temporarily loses internet connectivity, or if the licensing server experiences temporary maintenance, Content Engine Pro does not immediately lock or break your active automations.
* The plugin enters `connection_error` status and grants a 14-day grace window. All Pro automations, schedules, and queue items continue running normally.
* When connectivity is re-established during the weekly background sync, the status automatically returns to `active`.
* *Security Safeguard*: The offline grace period cannot be used to artificially extend an expired subscription whose chronological expiration date has already passed.

---

## 5. What Happens When a License Expires?

Content Engine Pro enforces a strict **Zero Data Lock-In** policy:

* **Published Posts**: Every post, article, and lesson created by the plugin remains 100% untouched in your WordPress database (`wp_posts`).
* **Schedules & Rules**: Your schedule definitions, prompt templates, and provider configurations remain saved in your database.
* **Graceful Degradation**: When a license expires, scheduled background cron processing pauses gracefully. Manual generation defaults to the Free Plan limits (6 articles per month, up to 700 words).
* **No Database Wiping**: The plugin never wipes or corrupts your database upon subscription expiration. Upon renewing your key, all paused schedules can be resumed immediately.

---

## 6. How to Upgrade to Pro

1. Visit the official purchase page: [https://code-elta6ur.net/shop/content-engine-pro](https://code-elta6ur.net/shop/content-engine-pro).
2. Complete your purchase securely. You will receive your license key (format: `CEPRO-XXXX-XXXX-XXXX-XXXX`).
3. In your WordPress admin, navigate to **Content Engine** -> **License**.
4. Paste your license key and click **Activate License**.
5. All Pro features, unlimited generation quotas, and background cron processing unlock instantly.
