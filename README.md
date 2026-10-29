# Content Engine Pro

**Autonomous, Multi-Provider AI Content Orchestration and Publishing Engine for WordPress**

[![Version](https://img.shields.io/badge/version-2.0.0-blue.svg)](CHANGELOG.md)
[![WordPress](https://img.shields.io/badge/WordPress-6.0--6.7-blue.svg)](docs/requirements.md)
[![PHP](https://img.shields.io/badge/PHP-8.1--8.2+-purple.svg)](docs/requirements.md)
[![License](https://img.shields.io/badge/License-Proprietary%20%2F%20Commercial-red.svg)](PROPRIETARY-NOTICE.md)
[![Tests](https://img.shields.io/badge/QA%20Tests-328%20Passed-brightgreen.svg)](docs/development-notes.md)
[![Arabic & RTL](https://img.shields.io/badge/Arabic-Native%20RTL-success.svg)](README.ar.md)

**Official Product Page & Purchase**: [https://code-elta6ur.net/shop/content-engine-pro](https://code-elta6ur.net/shop/content-engine-pro)  
**Publisher**: [Code Elta6ur](https://code-elta6ur.net/)  
**Lead Developer**: [Mohammad Abu Khashreef](https://mohammad.code-elta6ur.com/)  
**GitHub Repository**: [https://github.com/mokashreef/Content-Engine-Pro](https://github.com/mokashreef/Content-Engine-Pro)  
**Language Versions**: **English** | **[العربية (Arabic)](README.ar.md)**

---

## Table of Contents

- [Overview](#overview)
- [The Editorial Problem It Solves](#the-editorial-problem-it-solves)
- [Key Features](#key-features)
- [System Architecture](#system-architecture)
- [Free vs. Pro Comparison](#free-vs-pro-comparison)
- [Installation & First Use](#installation--first-use)
- [Requirements & Compatibility](#requirements--compatibility)
- [Documentation Index](#documentation-index)
- [Screenshot & Asset Checklist](#screenshot--asset-checklist)
- [Commercial Call to Action](#commercial-call-to-action)
- [License & Proprietary Notice](#license--proprietary-notice)

---

## Overview

**Content Engine Pro** is an autonomous content generation, structure, and scheduling engine built natively for WordPress. Developed under the **Code Elta6ur** commercial software brand, it bridges the gap between raw AI inference models and production WordPress editorial workflows.

Rather than generating isolated text snippets that require hours of manual formatting, copying, and configuring, Content Engine Pro acts as an internal editorial desk. It connects directly to leading AI inference providers—including generous free tiers like Google Gemini, Groq Cloud, OpenRouter, Mistral AI, Cerebras Cloud, and local Ollama models—and automates long-form writing (up to 6,000 words), zero-token local Gutenberg block assembly, automated internal linking, schema-compliant FAQ markup, multi-lesson course curriculums, and minutely scheduled publishing.

---

## The Editorial Problem It Solves

Interacting directly with conversational chatbot web interfaces solves only a fraction of the digital publishing lifecycle:

* **Token Output Boundaries**: Standard web chatbots cut off after 800–1,200 words, producing shallow content or requiring disjointed manual continuation prompts.
* **Formatting Friction**: Raw text must be manually cleaned, formatted with headings, and converted into valid WordPress Gutenberg blocks.
* **SEO Overhead**: Search optimization requires manual research, writing meta descriptions, identifying focus keywords, and assembling JSON-LD structured data.
* **Internal Linking Labor**: Cross-referencing existing site articles requires searching archives and manually inserting contextual links.
* **Publishing Consistency**: Maintaining a rigid editorial calendar requires manual scheduling and calendar tracking.

**Content Engine Pro automates the remaining 85% of the editorial workflow directly inside WordPress.**

---

## Key Features

### 1. Multi-Provider AI Inference & Dynamic Fallback
* **Direct HTTPS Connection**: Interfaces directly with Google Gemini, Groq Cloud, OpenRouter, Mistral AI, Cerebras Cloud, and self-hosted Ollama without proprietary cloud middlemen.
* **Automatic 429 Failover**: Automatically transitions to the next priority provider if your primary provider encounters rate limits.
* **Provider Lock Per Job**: Locks the selected provider to the active article job to preserve stylistic consistency across all sections.
* **Dynamic Model Discovery**: Queries provider endpoints in real time to populate compatible models in your admin dropdown.
* *Detailed Documentation*: [docs/features.md](docs/features.md#1-ai-provider-ecosystem--dynamic-fallback)

### 2. Long-Form Sequential Chunker (Up to 6,000 Words)
* **Algorithmic Section Scaling**: Automatically calculates optimal section counts based on requested depth (from 3 sections for 600 words up to 12 sections for 6,000 words).
* **Contextual Memory Handoff**: Passes concise summaries of earlier sections into subsequent section prompts to eliminate repetition and maintain narrative flow.
* **Zero-Token Local Assembly**: Server-side PHP compiles, validates, and standardizes Gutenberg blocks (`<!-- wp:heading -->`, `<!-- wp:paragraph -->`, `<!-- wp:list -->`, `<!-- wp:code -->`, `<!-- wp:table -->`), saving your token quotas for generation.
* **State Preservation**: If an API timeout occurs midway, completed sections remain saved in the database queue and resume seamlessly on retry.
* *Detailed Documentation*: [docs/features.md](docs/features.md#2-long-form-sequential-chunker--local-assembly)

### 3. Context-Aware Internal Linking Engine
* **Automated Candidate Discovery**: Scans up to 50 of your published WordPress posts and tags to identify relevant cross-linking opportunities.
* **Safe Paragraph Injection**: Inserts natural keyword anchors into standard paragraph tags (`<p>`) without breaking headings, blockquotes, code blocks, or existing links.
* *Detailed Documentation*: [docs/features.md](docs/features.md#3-context-aware-internal-linking-engine)

### 4. SEO Automation & JSON-LD FAQ Schema
* **Complete Metadata**: Generates SEO titles (<= 60 chars), meta descriptions (<= 155 chars), primary focus keywords, secondary keywords, and clean URL slugs.
* **FAQPage Schema**: Automatically synthesizes 3 targeted Question-and-Answer pairs formatted as validated JSON-LD schema with script injection hardening (`JSON_HEX_TAG | JSON_HEX_AMP`).
* **Cross-Plugin Compatibility**: Automatically detects and integrates with **Yoast SEO**, **Rank Math**, **All in One SEO (AIOSEO)**, or falls back to native `<meta>` output in `wp_head`.
* *Detailed Documentation*: [docs/features.md](docs/features.md#4-automated-seo--structured-data-faq-schema)

### 5. Educational Course & Curriculum Series
* **Dedicated Custom Post Type**: Manages structured multi-lesson curriculums through the built-in `cat_series` post type.
* **AI Syllabus Generator**: Synthesizes a structured multi-lesson outline from a single course topic.
* **Progressive Lesson Context**: When generating advanced lessons, the prompt supplies earlier lesson topics, ensuring concepts build progressively without redundancy.
* *Detailed Documentation*: [docs/features.md](docs/features.md#5-course-curriculums--educational-content-series)

### 6. Asynchronous Background Queue & WP-Cron Scheduling
* **Dedicated Database Tables**: Manages queue execution (`wp_cta_ce_queue`) and recurring rules (`wp_cta_ce_schedules`) with `dbDelta` schema migration support.
* **Minutely Cron Execution**: Asynchronous worker running every 60 seconds with 5-minute transient concurrency locks to prevent overlapping executions.
* **Exponential Backoff**: Three-tier retry engine (5m, 15m, 30m) handles transient network dropouts gracefully without crashing PHP.
* **Shared Hosting Protection**: Configurable batch limit (default: 2 jobs per minute, maximum: 5) ensures low CPU and memory footprints.
* *Detailed Documentation*: [docs/features.md](docs/features.md#6-asynchronous-background-queue--wp-cron-scheduling)

---

## System Architecture

```
[ WordPress Admin / WP-Cron ]
           │
           ▼
[ Queue Manager (`wp_cta_ce_queue`) ] ─── Concurrency Lock & Batch Control
           │
           ▼
[ Provider Manager & Fallback Engine ] ─── Locks Provider per Job
   ├── Google Gemini (gemini-1.5-flash)
   ├── Groq Cloud (llama-3.3-70b-versatile)
   ├── OpenRouter (meta-llama/llama-3.3-70b-instruct:free)
   ├── Mistral AI (mistral-small-latest)
   ├── Cerebras Cloud (llama3.1-70b)
   └── Custom / Local (Ollama on localhost:11434)
           │
           ▼
[ Sequential Chunker Engine ] ─── 3 to 12 Sections (Up to 6,000 Words)
           │
           ▼
[ Local Assembly & Gutenberg Converter (PHP) ]
   ├── Gutenberg Block Formatting (Heading, Paragraph, List, Code, Table)
   ├── Context-Aware Internal Linking (Scans existing posts & tags)
   ├── Quality Checks (Word count validation, heading hierarchy)
   └── SEO Metadata & FAQ Schema Generation (JSON-LD)
           │
           ▼
[ WordPress Database ] ─── Saved as Draft (`wp_posts`) + Revision History
```

---

## Free vs. Pro Comparison

Content Engine Pro provides a functional **Free Plan** for evaluation and light blogging, alongside a commercial **Pro Plan** ($10 USD / Year) for autonomous publishing.

| Feature / Capability | Free Plan | Content Engine Pro | Status in Code |
| :--- | :---: | :---: | :--- |
| **Monthly Generation Allowance** | **6 articles / month** | **Unlimited** | Implemented (`FREE_MONTHLY_ARTICLE_LIMIT = 6`) |
| **Max Word Count Per Article** | **700 words** | **Up to 6,000 words** | Implemented (`FREE_MAX_WORD_COUNT = 700`) |
| **Manual Article Generator** | Included | Included | Implemented |
| **AI Provider Connections** | Included | Included | Gemini, Groq, OpenRouter, Mistral, Cerebras, Ollama |
| **Basic SEO Metadata** | Included | Included | Title, description, focus keyword |
| **Long-Form Sequential Chunker** | Limited (Max 700w) | **Included (Up to 6,000w)** | Implemented |
| **Zero-Token Local PHP Assembly** | Included | Included | Implemented |
| **Automated Internal Linking** | Not Included | **Included** | Gated (`advanced_seo`) |
| **Automated FAQ JSON-LD Schema** | Not Included | **Included** | Gated (`advanced_seo`) |
| **WP-Cron Minutely Automation** | Not Included | **Included** | Gated (`automation`) |
| **Recurring Schedule Rules** | Not Included | **Included** | Gated (`automation`) |
| **Multi-Topic Batch Queue** | Not Included | **Included** | Gated (`bulk_generation`) |
| **Educational Courses & Series** | Not Included | **Included** | Gated (`series_courses`) |
| **Automatic 429 Provider Failover** | Not Included | **Included** | Implemented |
| **Commercial License Transfer** | N/A | **Included** | One-click deactivation / move between sites |
| **Offline Resilience** | N/A | **14-Day Grace Period** | Implemented (`GRACE_PERIOD_DURATION = 1209600`) |

*For complete licensing and subscription details, see [docs/editions.md](docs/editions.md).*

---

## Installation & First Use

### 1. Purchase and Download
Purchase your official commercial license key and download the production ZIP archive from the official store:  
👉 **[https://code-elta6ur.net/shop/content-engine-pro](https://code-elta6ur.net/shop/content-engine-pro)**

### 2. Install on WordPress
1. In your WordPress admin, go to **Plugins** -> **Add New Plugin** -> **Upload Plugin**.
2. Select `code-elta6ur-content-engine.zip` and click **Install Now**.
3. Click **Activate Plugin**.

### 3. Activate Your License
1. Navigate to **Content Engine** -> **License** ("الترخيص التجاري") in the sidebar.
2. Enter your commercial license key (`CEPRO-XXXX-XXXX-XXXX-XXXX`) and click **Activate License**.

### 4. Configure Your First AI Provider (Free Option)
1. Navigate to **Content Engine** -> **Providers (AI)**.
2. Under **Google Gemini** or **Groq Cloud**, paste your free API key.
3. Click **Discover Models**, select your model, set status to **Active**, and click **Save Settings**.
4. Click **Test Connection** to confirm network reachability.

### 5. Generate Your First Article
1. Navigate to **Content Engine** -> **Create Article** ("إنشاء مقال").
2. Enter your topic, select target word count, choose language, and click **Start Intelligent Generation**.
3. Follow the live progress monitor as the engine generates outlines, writes sections, and saves your formatted draft post.

*For detailed setup steps, consult the [Installation Guide](docs/installation.md) and [Getting Started Tutorial](docs/getting-started.md).*

---

## Requirements & Compatibility

| Prerequisite | Minimum Required | Tested & Recommended |
| :--- | :--- | :--- |
| **WordPress** | **6.0** | **6.0 through 6.7** |
| **PHP** | **8.1** | **8.1 through 8.2+** *(Tested on 8.2.33)* |
| **Database** | MySQL 5.7+ / MariaDB 10.3+ | MySQL 8.0+ / MariaDB 10.6+ |
| **Required PHP Extensions** | `curl`, `json`, `mbstring`, `openssl`, `pcre` | Standard in modern hosting |
| **WP-Cron** | Standard WP-Cron | Real system cron recommended for high-volume sites |

*Full compatibility guidelines available in [docs/requirements.md](docs/requirements.md).*

---

## Documentation Index

Explore our comprehensive technical documentation:

| Document | Purpose |
| :--- | :--- |
| [**Product Overview**](docs/overview.md) | High-level summary of architecture, design principles, and problem solved. |
| [**Features Catalog**](docs/features.md) | Exhaustive breakdown of every verified capability. |
| [**Editions & Licensing**](docs/editions.md) | Detailed Free vs. Pro comparison, pricing, and grace period rules. |
| [**System Requirements**](docs/requirements.md) | Prerequisites, required PHP extensions, and hosting recommendations. |
| [**Installation Guide**](docs/installation.md) | Supported installation, update, and clean uninstall procedures. |
| [**Getting Started**](docs/getting-started.md) | First-session walkthrough from API key setup to published draft. |
| [**Admin User Guide**](docs/user-guide.md) | In-depth guide to all admin screens, controls, and parameters. |
| [**Configuration Reference**](docs/configuration.md) | WordPress options schema, constants, and developer filter hooks. |
| [**Integrations Reference**](docs/integrations.md) | External API endpoints, SEO plugins, and Gutenberg block structures. |
| [**Publishing Workflows**](docs/workflows.md) | Sequence diagrams and architectural flows for on-demand and recurring jobs. |
| [**Troubleshooting Guide**](docs/troubleshooting.md) | Error codes, diagnostic steps, rate limit resolutions, and cron fixes. |
| [**Security & Privacy**](docs/security-and-privacy.md) | Auditable data transmission inventory, secret scrubbing, and CSRF defense. |
| [**Licensing & Usage**](docs/licensing-and-usage.md) | Commercial product model, domain transfer terms, and legal notices. |
| [**FAQ**](docs/faq.md) | Frequently asked questions regarding pricing, models, and data sovereignty. |
| [**Product Roadmap**](docs/roadmap.md) | Verified evolution items and feature request process. |
| [**Customer Support**](docs/support.md) | Verified support channels, diagnostic reporting, and confidentiality policy. |
| [**Developer Notes**](docs/development-notes.md) | Design patterns, automated QA test harness, and coding standards. |
| [**Arabic Documentation**](README.ar.md) | الدليل التوثيقي الكامل باللغة العربية الفصحى. |

---

## Screenshot & Asset Checklist

To preserve confidentiality and maintain high presentation standards, screenshots must be captured from a clean, staging WordPress installation without exposing private API keys or personal site details.

### Recommended Capture Checklist for Product Owner:
- [ ] **01_dashboard.png**: Central dashboard showing article metrics, license status, and quick generation widget.
- [ ] **02_generator_progress.png**: Article generator displaying the real-time 6-stage generation progress bar.
- [ ] **03_gutenberg_editor.png**: WordPress Gutenberg editor showcasing a generated article with clean headings, lists, and injected internal links.
- [ ] **04_providers_grid.png**: AI Providers screen showing active cards (Gemini, Groq, OpenRouter) and priority drag-and-drop handles.
- [ ] **05_series_curriculum.png**: Course curriculum editor (`cat_series`) showing multi-lesson syllabus structure.
- [ ] **06_publishing_calendar.png**: Monthly calendar view displaying color-coded scheduled and completed articles.
- [ ] **07_queue_manager.png**: Task queue table showing background jobs, attempt counters, and retry backoff states.
- [ ] **08_license_screen.png**: Commercial licensing screen showing active Pro subscription and 14-day grace period status.

---

## Commercial Call to Action

Unlock the full autonomous power of your WordPress content strategy with **Content Engine Pro**.

* **Unlimited Article Generations**
* **Long-Form Sequential Chunking up to 6,000 Words**
* **Minutely WP-Cron Background Automation**
* **Automated Internal Linking & FAQ Schema**
* **Multi-Lesson Course Curriculums**
* **Automatic Rate Limit Failover**
* **Resilient 14-Day Offline Grace Period**

👉 **[Purchase Content Engine Pro Today — $10 USD / Year](https://code-elta6ur.net/shop/content-engine-pro)**  
*Instant License Key Delivery • 1-Site License with Easy Domain Transfer • Zero Data Lock-In*

---

## License & Proprietary Notice

Copyright (c) 2026 Mohammad Abu Khashreef / Code Elta6ur. All rights reserved.

Content Engine Pro is a commercial proprietary product published by **Code Elta6ur** ([https://code-elta6ur.net/](https://code-elta6ur.net/)). 

Where required for compatibility with the WordPress platform, PHP code interacting directly with WordPress core APIs respects GNU General Public License (GPL) v2 or later obligations as stated in the plugin header. Commercial licensing keys, hosted licensing infrastructure, update delivery services, proprietary branding, trademarks, and premium support are proprietary commercial services operated by Code Elta6ur.

For complete terms, review [PROPRIETARY-NOTICE.md](PROPRIETARY-NOTICE.md) and [docs/licensing-and-usage.md](docs/licensing-and-usage.md).

