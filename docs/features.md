# Features Catalog — Content Engine Pro

This document provides a verified, code-accurate catalog of all capabilities implemented in **Content Engine Pro** (Version 2.0.0). Every feature listed here corresponds directly to classes, database schemas, and admin controllers in the plugin source.

---

## 1. AI Provider Ecosystem & Dynamic Fallback

Content Engine Pro connects directly to leading AI inference providers without routing traffic through third-party intermediaries. Site owners maintain full ownership of their API credentials and can utilize free-tier quotas.

| Provider | Default Model | Tier Type | Verified Integration Details |
| :--- | :--- | :--- | :--- |
| **Google Gemini** | `gemini-1.5-flash` | Free Tier / Pay-as-you-go | REST API integration with Gemini v1beta endpoint; supports model discovery, streaming-compatible token sizes, and system instructions. |
| **Groq Cloud** | `llama-3.3-70b-versatile` | Free Tier / High-Speed | OpenAI-compatible endpoint with sub-second inference speeds; handles aggressive rate limiting with automatic failover. |
| **OpenRouter** | `meta-llama/llama-3.3-70b-instruct:free` | Free & Paid Aggregator | Access to open-weight models with dynamic model discovery. |
| **Mistral AI** | `mistral-small-latest` | Free Experimentation / Paid | Native JSON mode and chat completions via Mistral v1 API. |
| **Cerebras Cloud** | `llama3.1-70b` | Free Tier / Ultra-Fast | Ultra-fast token inference for rapid outline and chunk generation. |
| **Custom / Local** | `llama3` (or user-defined) | Self-Hosted / Private | Compatible with local inference servers such as **Ollama**, **LM Studio**, vLLM, or private proxy servers (`http://localhost:11434/v1/chat/completions`). |

### Provider Management Features:
* **Priority-Based Failover**: Reorder providers via drag-and-drop in the admin dashboard (`wp_cta_ce_providers`). If the primary provider returns an HTTP 429 (Rate Limit) or network error, the engine automatically falls back to the next priority provider.
* **Provider Lock Per Job**: Once an article generation starts, the engine locks the active provider for the entire job lifecycle. This prevents voice and style shifts between sections of the same article.
* **Real-Time Connectivity Testing**: Interactive "Test Connection" button on each provider card verifies network reachability, API key validity, and response latency without triggering a full generation.
* **Model Discovery**: Dynamically queries each provider's model API to populate available models in the admin dropdown.

---

## 2. Long-Form Sequential Chunker & Local Assembly

Standard LLMs struggle to generate coherent content beyond 1,000–1,500 words due to output token boundaries. Content Engine Pro overcomes this through a multi-stage sequential chunking engine.

```
Requested Word Count ──► Outline Generation ──► Sequential Section Writing ──► Local Assembly in PHP
(e.g., 3,000 words)      (Calculates 7 sections)    (Each chunk receives summary   (Zero tokens wasted on
                                                    of prior sections)              formatting)
```

### Word Count Scaling Architecture:
* **<= 600 words**: 3 sections (Introduction, Core Body, Conclusion)
* **<= 1,200 words**: 4 sections
* **<= 1,800 words**: 5 sections
* **<= 2,600 words**: 7 sections
* **<= 4,000 words**: 9 sections
* **5,000 – 6,000 words**: 12 sections

### Sequential Context Memory:
When generating Section *N*, the prompt includes:
1. Complete article topic and target audience.
2. The full approved outline.
3. Specific section title and target section word count.
4. A concise, 1-sentence summary of each previously generated section. This maintains narrative continuity, prevents repetitive introductory phrases, and ensures logical progression.

### Zero-Token Local Assembly in PHP:
Rather than paying an AI provider to combine and format HTML, assembly is handled natively in server-side PHP (`CTA_CE_Chunker::assemble_locally`):
* Standardizes heading levels, ensuring all section titles render as valid H2 blocks.
* Converts raw text into valid WordPress Gutenberg block markup (`wp:heading`, `wp:paragraph`, `wp:list`, `wp:code`, `wp:table`).
* Computes actual word counts excluding Gutenberg HTML comments.

### State Preservation on Interruption:
If a server timeout or API outage occurs during section 5 of a 9-section article, sections 1 through 4 are preserved in the database queue payload. When the retry worker executes, it resumes generation directly at section 5 without re-generating completed sections.

---

## 3. Context-Aware Internal Linking Engine

The internal linking engine (`CTA_CE_Internal_Links`) automatically connects new content to your site's existing knowledge base to strengthen topical authority and SEO.

* **Automated Candidate Discovery**: Queries up to 50 of your site's published posts, indexing titles, permalinks, and WordPress tags.
* **Smart Anchor Text Matching**: Generates anchor permutations from post titles (full titles and multi-word combinations) and tags.
* **Safe Paragraph Injection**: Operates exclusively inside `<p>` paragraphs. It never breaks headings (`<h2>`, `<h3>`), blockquotes, code blocks, or existing hyperlinks.
* **Configurable Density**: Set a maximum link threshold (default: 5 links, configurable in settings). Deduplicates URLs so no article links to the same destination twice.

---

## 4. Automated SEO & Structured Data (FAQ Schema)

Content Engine Pro produces complete on-page SEO assets for every generated article (`CTA_CE_SEO`).

* **Metadata Generation**:
  * **SEO Title**: Engaging title constrained to <= 60 characters.
  * **Meta Description**: Compelling summary constrained to <= 155 characters.
  * **Focus Keyword**: Primary keyword aligned with user intent.
  * **Secondary Keywords**: Comma-separated semantic keywords.
  * **URL Slug**: Clean, sanitized permalink slug in Arabic or English.
* **JSON-LD FAQ Schema**: Automatically synthesizes 3 targeted Question-and-Answer pairs based on the article's core arguments. Formatted as `https://schema.org/FAQPage` structured data.
* **Cross-Plugin SEO Compatibility**:
  * **Yoast SEO**: Populates `_yoast_wpseo_title`, `_yoast_wpseo_metadesc`, `_yoast_wpseo_focuskw`.
  * **Rank Math**: Populates `rank_math_title`, `rank_math_description`, `rank_math_focus_keyword`.
  * **All in One SEO (AIOSEO)**: Populates `_aioseo_title`, `_aioseo_description`, `_aioseo_keywords`.
  * **Native Fallback**: If no third-party SEO plugin is active, Content Engine Pro automatically outputs `<meta name="description">` and the JSON-LD FAQ schema directly in `wp_head`.
* **Security Hardening**: Schema scripts are sanitized with `JSON_HEX_TAG | JSON_HEX_AMP` and `wp_kses_post()` to eliminate any possibility of stored XSS via generated markup.

---

## 5. Course Curriculums & Educational Content Series

Content Engine Pro includes a dedicated custom post type (`cat_series`) designed specifically for structured learning paths and multi-part guides.

* **Curriculum Planning by AI**: Enter a overarching course subject and desired lesson count; the engine drafts a comprehensive syllabus with structured lesson titles.
* **Visual Lesson Reordering**: Add, edit, remove, or drag-and-drop lessons in the admin before approving generation.
* **Cross-Lesson Context Awareness**: During lesson generation, the prompt supplies the full course syllabus and a list of previous lessons covered. This guarantees that advanced lessons build upon foundational concepts without repeating previous explanations.
* **Batch or Staggered Scheduling**: Publish the entire course immediately as drafts, or schedule lessons across specific days of the week (e.g., every Monday and Thursday at 10:00 AM).

---

## 6. Asynchronous Background Queue & WP-Cron Scheduling

The scheduling architecture protects web server performance through asynchronous background processing.

* **Dedicated Database Tables**:
  * `wp_cta_ce_queue`: Individual generation and outline jobs with state payloads, attempt counters, and error tracking.
  * `wp_cta_ce_schedules`: Recurring automation rules with defined days of the week, times, categories, and target lengths.
* **Minutely Worker (`cta_ce_every_minute`)**: Runs every 60 seconds via WP-Cron, protected by a 5-minute transient lock (`cta_ce_cron_running`) to prevent concurrent process collisions.
* **Shared-Hosting Batch Protection**: Processes a limited batch (default: 2 jobs per minute, maximum: 5) to keep memory usage and CPU execution times well below hosting thresholds.
* **Exponential Retry Backoff**: When an API error or server timeout occurs, the job transitions to `retry` status and reschedules itself across three configurable delays (default: 5 minutes, 15 minutes, 30 minutes). After 3 failed attempts, it gracefully marks the job as `failed` with a descriptive error message without crashing PHP.
* **Publishing Calendar**: An interactive monthly calendar view displays pending, scheduled, and completed articles with quick-access links.

---

## 7. Editorial Quality Control & Revision Snapshots

* **Pre-Publish Quality Validation (`CTA_CE_Quality_Check`)**:
  * Confirms actual word count falls within an acceptable tolerance of the target.
  * Validates heading structure and readability.
  * If critical quality warnings occur, the engine automatically forces the post status to `draft` and logs a notification for manual editorial review.
* **Revision Snapshots (`wp_cta_ce_history`)**: Stores full HTML snapshots and token metadata in a dedicated history table, allowing one-click version restoration from the admin interface.

---

## 8. Internationalization & Native Arabic RTL

* **Built-in Arabic & RTL Support**: Interface stylesheets (`admin-rtl.css`) provide a first-class right-to-left layout tailored for Arabic-speaking publishers.
* **International Translation Ready**: Full localization support through `code-elta6ur-content-engine.pot` and standard WordPress textdomain (`code-elta6ur-content-engine`).

---

## 9. Security, Sanitization & Audit Logging

* **Nonce Verification**: All admin actions and AJAX endpoints require secure WordPress Nonces (`CTA_CE_Security::verify_admin()`).
* **Capability Checks**: Restricts configuration, licensing, and import/export operations to users with `manage_options` capability.
* **Credential Masking**: API keys and commercial license keys are masked in both the admin interface and logs (e.g., `AIzaSy...cdef`, `CEPRO-****-****-****-5678`).
* **Automatic Log Sanitization & Rotation**:
  * All log entries scrub HTTP `Bearer` tokens and `api_key=` parameters before inserting into `wp_cta_ce_logs`.
  * Logs older than 30 days are automatically purged daily to prevent database bloat.
* **Data Portability**: Clean JSON export and import for plugin settings, provider preferences, and prompt templates.

