# Integrations Reference — Content Engine Pro

This document details all verified external services, WordPress ecosystem components, and third-party plugins that interface with **Content Engine Pro**.

---

## 1. Verified AI Inference Providers

Content Engine Pro connects directly from your WordPress server to external inference endpoints over HTTPS (TLS 1.3). No intermediate cloud broker or proprietary proxy is used.

### Supported External Providers:

```
[ Your WordPress Server ]
        │
        ├──► Google Gemini API (gemini-2.0-flash / gemini-2.5-flash)
        ├──► Groq Cloud API (llama-3.3-70b-versatile)
        ├──► OpenRouter API (meta-llama/llama-3.3-70b-instruct:free)
        ├──► Mistral AI API (mistral-small-latest)
        ├──► Cerebras Cloud API (llama3.1-70b)
        └──► Self-Hosted / Local LLM (Ollama / LM Studio on localhost:11434)
```

1. **Google Gemini**:
   * Endpoint: `https://generativelanguage.googleapis.com/v1beta/models/`
   * Protocol: REST JSON over HTTPS (default timeout: 90 seconds).
   * Authentication: API Key query parameter (`key=...`).
   * Features: Dynamic model listing, system instructions, temperature adjustment, filtering of internal reasoning (`thought`) parts on thinking models, and diagnostic `finishReason` / `blockReason` reporting when empty/blocked responses occur.
2. **Groq Cloud**:
   * Endpoint: `https://api.groq.com/openai/v1/chat/completions`
   * Protocol: OpenAI-compatible REST JSON over HTTPS (default timeout: 90 seconds).
   * Authentication: Bearer token (`Authorization: Bearer <API_KEY>`).
   * Features: Ultra-low latency inference, JSON response formatting, automatic rate-limit failover target.
3. **OpenRouter**:
   * Endpoint: `https://openrouter.ai/api/v1/chat/completions`
   * Protocol: OpenAI-compatible REST JSON over HTTPS (default timeout: 90 seconds).
   * Authentication: Bearer token with custom HTTP referer headers.
   * Features: Aggregated access to diverse open-source model checkpoints.
4. **Mistral AI**:
   * Endpoint: `https://api.mistral.ai/v1/chat/completions`
   * Protocol: REST JSON over HTTPS (default timeout: 90 seconds).
   * Authentication: Bearer token.
5. **Cerebras Cloud**:
   * Endpoint: `https://api.cerebras.ai/v1/chat/completions`
   * Protocol: OpenAI-compatible REST JSON over HTTPS (default timeout: 90 seconds).
   * Authentication: Bearer token.
6. **Local LLM / Custom Endpoint (Ollama / LM Studio)**:
   * Default Local URL: `http://localhost:11434/v1/chat/completions`
   * Protocol: Standard OpenAI-compatible format over local HTTP/HTTPS (default timeout: 120 seconds).
   * Features: Completely private, offline-capable generation without third-party API dependencies or fees.

> **Failover & Rate-Limit Pacing (v2.0.2+)**: `CTA_CE_Provider_Manager::generate_with_fallback()` switches immediately to the next configured provider on HTTP 429 or connection failure. When only a single provider is active on live WordPress servers, it pauses for a 6-second backoff retry before failing gracefully.

---

## 2. WordPress Core & Gutenberg Block Editor

Content Engine Pro is architected for native 100% WordPress 6.x Gutenberg Block Editor compatibility (`CTA_CE_Block_Converter` & `CTA_CE_Security`):

* **Native Block Comments & Grammar**: Generated content is compiled directly into official Gutenberg block serialization:
  * `<!-- wp:heading {"level":2} -->` with `class="wp-block-heading"`
  * `<!-- wp:paragraph -->`
  * `<!-- wp:list -->` / `<!-- wp:list {"ordered":true} -->` with `class="wp-block-list"` and nested `<!-- wp:list-item -->` blocks
  * `<!-- wp:code -->` with `class="wp-block-code"`
  * `<!-- wp:quote -->` with `class="wp-block-quote"`
  * `<!-- wp:table {"className":"is-style-stripes"} -->` with `<figure class="wp-block-table is-style-stripes"><table class="has-fixed-layout">`
  * `<!-- wp:separator -->` with `class="wp-block-separator has-alpha-channel-opacity"`
* **Mixed-Content Depth Normalization**: If an AI emits partial Gutenberg blocks alongside unwrapped HTML fragments, `normalize_existing_blocks()` walks block boundaries and wraps any bare fragments outside blocks using `convert_html_to_blocks()`.
* **Protected Block Persistence**: `CTA_CE_Security::insert_gutenberg_post()` protects `<!-- wp:... -->` delimiters from being stripped by `wp_kses_post()` and WP-Cron `wp_filter_post_kses` hooks so posts always open natively as editable Gutenberg blocks rather than a fallback Classic HTML block.
* **Zero Theme Conflicts**: Standard Gutenberg blocks render cleanly across Classic Themes, Hybrid Themes, and Full Site Editing (FSE) Block Themes.
* **Custom Post Types**: Registers the `cat_series` custom post type with `show_in_rest => true`, providing full Gutenberg editor support for course curriculums.

---

## 3. SEO Plugins & Structured Data

The SEO module (`CTA_CE_SEO`) automatically detects your active SEO plugin and updates the appropriate metadata fields:

| SEO Solution | Detection Mechanism | Meta Fields Updated |
| :--- | :--- | :--- |
| **Yoast SEO** | `defined('WPSEO_VERSION')` | `_yoast_wpseo_title`<br>`_yoast_wpseo_metadesc`<br>`_yoast_wpseo_focuskw` |
| **Rank Math** | `defined('RANK_MATH_VERSION')` | `rank_math_title`<br>`rank_math_description`<br>`rank_math_focus_keyword` |
| **All in One SEO (AIOSEO)** | `defined('AIOSEO_VERSION')` | `_aioseo_title`<br>`_aioseo_description`<br>`_aioseo_keywords` |
| **Native Fallback** | Fallback when no plugin is detected | Injects `<meta name="description">` and `<meta name="keywords">` directly into `wp_head`. |

### JSON-LD FAQ Schema:
In addition to plugin metadata, Content Engine Pro generates a validated `https://schema.org/FAQPage` script block:
* Hardened with `JSON_HEX_TAG | JSON_HEX_AMP` to prevent script injection.
* Injected into the frontend `wp_head` for all generated articles containing FAQ items.

---

## 4. Authoritative Commercial Licensing Service

Content Engine Pro connects securely to the Code Elta6ur licensing service:

* **Endpoint**: `https://code-elta6ur.net/api/v1/license/`
* **Security & Authentication**:
  * Outbound requests include an `X-CTA-Signature` computed via HMAC-SHA256 over the request payload using the site's unique installation UUID.
  * Verified TLS 1.3 encryption.
* **Actions Supported**:
  * `/activate`: Validates key, binds domain, and returns signed expiration token.
  * `/validate`: Performs periodic background status checks (throttled to every 3 days).
  * `/deactivate`: Releases domain activation slot to enable site migration.

---

## 5. Explicit Non-Integrations (Boundary Clarifications)

To prevent misunderstandings, the following capabilities are explicitly documented as **not present or unverified** in the current release:

* **External Image Generators**: An option exists in general settings (`enable_image_generation`), but image generation APIs (e.g., DALL-E, Midjourney, Stable Diffusion) are disabled and not connected in version 2.0.4.
* **Social Media Auto-Sharing**: The plugin does not post directly to Facebook, Twitter/X, or LinkedIn.
* **Third-Party SaaS Middlemen**: No generation data passes through any intermediate proprietary SaaS servers.

