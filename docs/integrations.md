# Integrations Reference — Content Engine Pro

This document details all verified external services, WordPress ecosystem components, and third-party plugins that interface with **Content Engine Pro**.

---

## 1. Verified AI Inference Providers

Content Engine Pro connects directly from your WordPress server to external inference endpoints over HTTPS (TLS 1.3). No intermediate cloud broker or proprietary proxy is used.

### Supported External Providers:

```
[ Your WordPress Server ]
        │
        ├──► Google Gemini API (gemini-1.5-flash / gemini-1.5-pro)
        ├──► Groq Cloud API (llama-3.3-70b-versatile)
        ├──► OpenRouter API (meta-llama/llama-3.3-70b-instruct:free)
        ├──► Mistral AI API (mistral-small-latest)
        ├──► Cerebras Cloud API (llama3.1-70b)
        └──► Self-Hosted / Local LLM (Ollama / LM Studio on localhost:11434)
```

1. **Google Gemini**:
   * Endpoint: `https://generativelanguage.googleapis.com/v1beta/models/`
   * Protocol: REST JSON over HTTPS.
   * Authentication: API Key query parameter (`key=...`) or `x-goog-api-key` header.
   * Features: Dynamic model listing, system instructions, temperature adjustment.
2. **Groq Cloud**:
   * Endpoint: `https://api.groq.com/openai/v1/chat/completions`
   * Protocol: OpenAI-compatible REST JSON over HTTPS.
   * Authentication: Bearer token (`Authorization: Bearer <API_KEY>`).
   * Features: Ultra-low latency inference, JSON response formatting.
3. **OpenRouter**:
   * Endpoint: `https://openrouter.ai/api/v1/chat/completions`
   * Protocol: OpenAI-compatible REST JSON over HTTPS.
   * Authentication: Bearer token with custom HTTP referer headers.
   * Features: Aggregated access to diverse open-source model checkpoints.
4. **Mistral AI**:
   * Endpoint: `https://api.mistral.ai/v1/chat/completions`
   * Protocol: REST JSON over HTTPS.
   * Authentication: Bearer token.
5. **Cerebras Cloud**:
   * Endpoint: `https://api.cerebras.ai/v1/chat/completions`
   * Protocol: OpenAI-compatible REST JSON over HTTPS.
   * Authentication: Bearer token.
6. **Local LLM / Custom Endpoint (Ollama / LM Studio)**:
   * Default Local URL: `http://localhost:11434/v1/chat/completions`
   * Protocol: Standard OpenAI-compatible format over local HTTP/HTTPS.
   * Features: Completely private, offline-capable generation without third-party API dependencies or fees.

---

## 2. WordPress Core & Gutenberg Block Editor

Content Engine Pro is architected for native compatibility with the WordPress Gutenberg Block Editor:

* **Native Block Comments**: Section content is compiled directly into Gutenberg comment syntax:
  * `<!-- wp:heading {"level":2} -->`
  * `<!-- wp:paragraph -->`
  * `<!-- wp:list -->`
  * `<!-- wp:code -->`
  * `<!-- wp:table -->`
* **Zero Theme Conflicts**: Because generated content consists of standard Gutenberg blocks, it renders flawlessly across Classic Themes, Hybrid Themes, and modern Full Site Editing (FSE) Block Themes.
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
  * `/validate`: Performs periodic background status checks (throttled to every 7 days).
  * `/deactivate`: Releases domain activation slot to enable site migration.

---

## 5. Explicit Non-Integrations (Boundary Clarifications)

To prevent misunderstandings, the following capabilities are explicitly documented as **not present or unverified** in the current release:

* **External Image Generators**: An option exists in general settings (`enable_image_generation`), but image generation APIs (e.g., DALL-E, Midjourney, Stable Diffusion) are disabled and not connected in version 2.0.0.
* **Social Media Auto-Sharing**: The plugin does not post directly to Facebook, Twitter/X, or LinkedIn.
* **Third-Party SaaS Middlemen**: No generation data passes through any intermediate proprietary SaaS servers.

