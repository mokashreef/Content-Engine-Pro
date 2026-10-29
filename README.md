# Content Engine Pro

**Autonomous, Multi-Provider AI Content Orchestration and Publishing Engine for WordPress**

[![Version](https://img.shields.io/badge/version-2.0.0-blue.svg)](CHANGELOG.md)
[![WordPress](https://img.shields.io/badge/WordPress-6.0--6.7-blue.svg)](docs/requirements.md)
[![PHP](https://img.shields.io/badge/PHP-8.1--8.2+-purple.svg)](docs/requirements.md)
[![License](https://img.shields.io/badge/License-Proprietary%20%2F%20Commercial-red.svg)](PROPRIETARY-NOTICE.md)
[![Tests](https://img.shields.io/badge/QA%20Tests-328%20Passed-brightgreen.svg)](docs/development-notes.md)
[![Issues](https://img.shields.io/github/issues/mokashreef/Content-Engine-Pro.svg?color=brightgreen)](https://github.com/mokashreef/Content-Engine-Pro/issues)
[![Pull Requests](https://img.shields.io/github/issues-pr/mokashreef/Content-Engine-Pro.svg?color=blue)](https://github.com/mokashreef/Content-Engine-Pro/pulls)
[![Bilingual](https://img.shields.io/badge/Documentation-English%20%7C%20العربية-success.svg)](#-النسخة-العربية---arabic-version)

**Official Product Page & Purchase**: [https://code-elta6ur.net/shop/content-engine-pro](https://code-elta6ur.net/shop/content-engine-pro)  
**Publisher**: [Code Elta6ur](https://code-elta6ur.net/)  
**Lead Developer**: [Mohammad Abu Khashreef](https://mohammad.code-elta6ur.com/)  
**GitHub Repository**: [https://github.com/mokashreef/Content-Engine-Pro](https://github.com/mokashreef/Content-Engine-Pro)  

---

> 🌐 **Language / اللغة**: **English (Primary / الأساس)** • **[الانتقال إلى النسخة العربية (Jump to Arabic Version)](#-النسخة-العربية---arabic-version)** • **[ملف العربية المنفصل (README.ar.md)](README.ar.md)**

---

## Table of Contents (English)

- [Overview](#overview)
- [The Editorial Problem It Solves](#the-editorial-problem-it-solves)
- [Key Features](#key-features)
- [System Architecture](#system-architecture)
- [Free vs. Pro Comparison](#free-vs-pro-comparison)
- [Installation & First Use](#installation--first-use)
- [Requirements & Compatibility](#requirements--compatibility)
- [Documentation Index](#documentation-index)
- [Issues & Bug Tracking](#issues--bug-tracking)
- [Pull Requests & Contributions](#pull-requests--contributions)
- [Screenshot & Asset Checklist](#screenshot--asset-checklist)
- [Commercial Call to Action](#commercial-call-to-action)
- [License & Proprietary Notice](#license--proprietary-notice)
- [**🇸🇦 النسخة العربية (Arabic Version)**](#-النسخة-العربية---arabic-version)

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

---

## Issues & Bug Tracking

We track all public software bugs, environment-specific issues, and community feature proposals directly on GitHub:

* 📋 [**Browse All Issues**](https://github.com/mokashreef/Content-Engine-Pro/issues): Search active discussions, known issues, and planned investigations.
* 🐛 [**Submit a Bug Report**](https://github.com/mokashreef/Content-Engine-Pro/issues/new?template=bug_report.yml): Report a reproducible error using our structured form (gathers WordPress/PHP versions, active provider, and redacted logs).
* 💡 [**Request a Feature**](https://github.com/mokashreef/Content-Engine-Pro/issues/new?template=feature_request.yml): Suggest a new editorial workflow, AI integration, or architecture improvement.
* 🔒 **Private Support & Billing**: For commercial license key resets, order support, or private site debugging, please contact [support@code-elta6ur.net](mailto:support@code-elta6ur.net) directly.

---

## Pull Requests & Contributions

Community contributions to documentation, translation accuracy, and verified bug fixes are welcome:

* 🔀 [**Browse Pull Requests**](https://github.com/mokashreef/Content-Engine-Pro/pulls): Review active community contributions and documentation patches.
* 🚀 [**Open a Pull Request**](https://github.com/mokashreef/Content-Engine-Pro/compare): Propose improvements targeting the `main` branch.
* 📖 [**Contributing Guidelines**](CONTRIBUTING.md): Please review our quality standards and commercial code policies before submitting code or documentation changes.
* 🛡️ [**Code of Conduct**](CODE_OF_CONDUCT.md): Our pledge to maintain a welcoming, professional collaboration environment.

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

---
---

# 🇸🇦 النسخة العربية - Arabic Version

[العودة إلى بداية المستند بالإنجليزية (Back to Top / English)](#content-engine-pro) • [عرض كملف منفصل (README.ar.md)](README.ar.md)

---

# محرك المحتوى الاحترافي — Content Engine Pro

**المحرك الذكي المتكامل والأول من نوعه داخل ووردبريس لأتمتة صناعة وهيكلة وجدولة ونشر المقالات والسلاسل التعليمية بالذكاء الاصطناعي**

[![Version](https://img.shields.io/badge/الإصدار-2.0.0-blue.svg)](CHANGELOG.md)
[![WordPress](https://img.shields.io/badge/ووردبريس-6.0--6.7-blue.svg)](docs/requirements.md)
[![PHP](https://img.shields.io/badge/PHP-8.1--8.2+-purple.svg)](docs/requirements.md)
[![License](https://img.shields.io/badge/الترخيص-تجاري%20%2F%20Code%20Elta6ur-red.svg)](PROPRIETARY-NOTICE.md)
[![Tests](https://img.shields.io/badge/فحوصات%20الجودة-328%20ناجح-brightgreen.svg)](docs/development-notes.md)
[![Issues](https://img.shields.io/github/issues/mokashreef/Content-Engine-Pro.svg?color=brightgreen)](https://github.com/mokashreef/Content-Engine-Pro/issues)
[![Pull Requests](https://img.shields.io/github/issues-pr/mokashreef/Content-Engine-Pro.svg?color=blue)](https://github.com/mokashreef/Content-Engine-Pro/pulls)
[![اللغة](https://img.shields.io/badge/اللغة-عربي%20أصيل%20RTL-success.svg)](README.ar.md)

**الصفحة الرسمية وشراء الترخيص**: [https://code-elta6ur.net/shop/content-engine-pro](https://code-elta6ur.net/shop/content-engine-pro)  
**الشركة المطورة**: [Code Elta6ur (كود التطور)](https://code-elta6ur.net/)  
**المطور الرئيسي**: [المهندس محمد أبو خشريف](https://mohammad.code-elta6ur.com/)  
**مستودع GitHub**: [https://github.com/mokashreef/Content-Engine-Pro](https://github.com/mokashreef/Content-Engine-Pro)  

---

## فهرس المحتويات (بالعربية)

- [نظرة عامة](#نظرة-عامة-1)
- [المشكلة التحريرية التي يحلها المحرك](#المشكلة-التحريرية-التي-يحلها-المحرك-1)
- [المميزات الرئيسية الموثقة](#المميزات-الرئيسية-الموثقة-1)
- [الهيكلية البرمجية للنظام](#الهيكلية-البرمجية-للنظام-1)
- [مقارنة الخطة المجانية بالنسخة الاحترافية (Pro)](#مقارنة-الخطة-المجانية-بالنسخة-الاحترافية-pro-1)
- [التنصيب والتشغيل لأول مرة](#التنصيب-والتشغيل-لأول-مرة-1)
- [المتطلبات والتوافق البرمجي](#المتطلبات-والتوافق-البرمجي-1)
- [دليل التوثيق الشامل](#دليل-التوثيق-الشامل-1)
- [البلاغات وتتبع المشكلات (Issues)](#البلاغات-وتتبع-المشكلات-issues)
- [طلبات الدمج والمساهمة (Pull Requests)](#طلبات-الدمج-والمساهمة-pull-requests)
- [الشراء والاشتراك التجاري](#الشراء-والاشتراك-التجاري-1)
- [بيان الملكية وحقوق الاستخدام](#بيان-الملكية-وحقوق-الاستخدام-1)

---

## نظرة عامة

**Content Engine Pro** هو محرك ووردبريس تجاري متكامل ومستقل تم بناؤه وتطويره تحت مظلة **Code Elta6ur** لسد الفجوة الكبيرة بين نماذج الذكاء الاصطناعي الخام ومتطلبات النشر الاحترافي داخل مواقع ووردبريس.

بدلاً من الاكتفاء بتوليد نصوص عشوائية تتطلب ساعات من النسخ والتنسيق وإصلاح وسوم HTML والبحث عن روابط داخلية، يعمل المحرك كمكتب تحرير آلي كامل داخل موقعك. يتصل المحرك مباشرة بمزودي الذكاء الاصطناعي العالميين—بما في ذلك الباقات المجانية الرائدة مثل Google Gemini و Groq Cloud و OpenRouter و Mistral AI و Cerebras، بالإضافة إلى النماذج المحلية عبر Ollama—ويتولى كتابة المقالات الطويلة المقسمة ذكياً حتى 6,000 كلمة، والتجميع المحلي فائق السرعة عبر PHP دون استهلاك أي رصيد AI، والربط الداخلي التلقائي، وهيكلة بيانات الأسئلة الشائعة (FAQ Schema)، وبناء الكورسات والسلاسل التعليمية، والجدولة الآلية كل دقيقة في الخلفية عبر WP-Cron.

---

## المشكلة التحريرية التي يحلها المحرك

الاعتماد التقليدي على روبوتات الدردشة (مثل ChatGPT أو Gemini عبر المتصفح) لا يغطي سوى 15% فقط من دورة النشر الإلكتروني:

* **انقطاع النصوص الطويلة**: تتوقف المحادثات العادية بعد 800 إلى 1,200 كلمة بسبب قيود التوكنز، مما ينتج مقالات مقتضبة وغير شاملة.
* **إجهاد التنسيق**: تضطر يدوياً لنسخ النصوص، وتنسيق العناوين H2/H3، وتعديل القوائم وتحويلها إلى مكونات جوتنبرج (Gutenberg Blocks).
* **أعباء السيو (SEO)**: يلزمك يدوياً كتابة أوصاف الميتا، وتحديد الكلمات المفتاحية، وتوليد أكواد Schema JSON-LD.
* **عناء الروابط الداخلية**: يستغرق البحث عن مقالات سابقة مناسبة داخل موقعك وإدراجها وقتاً طويلاً.
* **فوضى الجدولة**: تتطلب مواصلة النشر التزاماً بشرياً يومياً ومتابعة مستمرة لتقويم الموقع.

**يتولى Content Engine Pro أتمتة الـ 85% المتبقية من العمل التحريري بالكامل داخل ووردبريس.**

---

## المميزات الرئيسية الموثقة

### 1. استقلال تام وتعدد للمزودين مع التبديل التلقائي (Auto-Failover)
* **اتصال مشفر مباشر**: اتصال HTTPS مباشر بين خادمك ومزودي الـ AI دون المرور بأي خوادم وسيطة خارجية.
* **تبديل فوري عند تجاوز الحد (429 Rate Limit)**: في حال واجه المزود الأول ضغطاً أو استنفاداً لحدوده المجانية، ينتقل المحرك تلقائياً للمزود التالي في قائمة الأولوية.
* **تثبيت المزود للمهمة (Provider Lock)**: بمجرد بدء توليد المقال، يتم تثبيت المزود لضمان اتساق النبرة والأسلوب عبر كافة أجزاء المقال.
* **استكشاف النماذج الحية**: استعلام مباشر لنقاط نهاية المزودين لجلب أحدث الموديلات المتوفرة وتحديث القوائم المنسدلة تلقائياً.
* *التوثيق المفصل*: [docs/features.md](docs/features.md#1-ai-provider-ecosystem--dynamic-fallback)

### 2. محرك التقسيم الذكي حتى 6,000 كلمة (Sequential Chunker)
* **حساب رياضي لعدد الأقسام**: احتساب تلقائي لعدد الأقسام بناءً على الطول المطلوب (من 3 أقسام لمقال 600 كلمة وحتى 12 قسماً لمقال 6,000 كلمة).
* **ذاكرة السياق التتابعي (Context Memory)**: يمرر المحرك ملخصاً مكثفاً للأقسام المكتوبة سابقاً إلى البرومبت الخاص بالقسم الجديد لضمان تسلسل الأفكار ومنع أي تكرار أو حشو.
* **تجميع محلي مجاني في PHP (Zero-Token Assembly)**: يتولى كود PHP تجميع المقال، وضبط هيكلية العناوين، وتحويل العناصر إلى مكونات جوتنبرج أصلية (`wp:heading`، `wp:paragraph`، `wp:list`، `wp:code`، `wp:table`) دون إهدار توكنز الـ AI.
* **حفظ الحالة عند الانقطاع**: لو انقطع الاتصال عند القسم الخامس من مقال مكون من 8 أقسام، تظل الأقسام الأربعة الأولى محفوظة في قاعدة البيانات ويستأنف التوليد من القسم الخامس تلقائياً.
* *التوثيق المفصل*: [docs/features.md](docs/features.md#2-long-form-sequential-chunker--local-assembly)

### 3. محرك الربط الداخلي الذكي والسياقي
* **استكشاف تلقائي للمحتوى**: فحص دوري لـ 50 مقالاً منشوراً في موقعك وفهرسة العناوين والوسوم والروابط الدائمة.
* **إدراج طبيعي وآمن**: إدراج روابط النصوص المفتاحية داخل فقرات `<p>` فقط، مع حماية تامة للعناوين، والاقتباسات، والأكواد، والروابط السابقة.
* *التوثيق المفصل*: [docs/features.md](docs/features.md#3-context-aware-internal-linking-engine)

### 4. أتمتة السيو وبيانات الأسئلة الشائعة (FAQ Schema)
* **توليد سيو متكامل**: عناوين سيو لا تتجاوز 60 حرفاً، وأوصاف ميتا لا تتجاوز 155 حرفاً، وكلمات مفتاحية، وروابط مخصصة (Slugs).
* **كود Schema JSON-LD مؤمن**: توليد 3 أسئلة شائعة مطابقة لمعايير `schema.org/FAQPage` مع حماية صارمة ضد ثغرات XSS باستخدام `JSON_HEX_TAG | JSON_HEX_AMP`.
* **توافق تلقائي مع أشهر إضافات السيو**: كشف وتخزين مباشر في حقول **Yoast SEO** و **Rank Math** و **All in One SEO** أو الإخراج التلقائي المدمج في `wp_head`.
* *التوثيق المفصل*: [docs/features.md](docs/features.md#4-automated-seo--structured-data-faq-schema)

### 5. دورات وسلاسل تعليمية تفاعلية (Content Series)
* **نوع منشورات مخصص `cat_series`**: إدارة مستقلة ومحترفة للمسارات التعليمية والكورسات داخل ووردبريس.
* **توليد خطة الدروس بالـ AI**: إدخال عنوان الكورس وعدد الدروس لتوليد خطة متكاملة فورياً.
* **تدرج معرفي ذكي**: أثناء كتابة أي درس، يتلقى الذكاء الاصطناعي ما تم شرحه في الدروس السابقة لبناء المعلومة تدريجياً دون إعادة شرح الأساسيات.
* *التوثيق المفصل*: [docs/features.md](docs/features.md#5-course-curriculums--educational-content-series)

### 6. طابور مهام مرن وجدولة WP-Cron منضبطة
* **جداول قاعدة بيانات مخصصة**: إدارة الطابور (`wp_cta_ce_queue`) وقواعد الجدولة (`wp_cta_ce_schedules`) مع توافق `dbDelta`.
* **جدولة دقيقة بالخلفية**: حدث cron يعمل كل 60 ثانية مع قفل مؤقت (5 دقائق) لمنع تضارب العمليات.
* **إعادة المحاولة التراكمية (Exponential Backoff)**: إعادة المحاولة تلقائياً بعد 5 دقائق، ثم 15 دقيقة، ثم 30 دقيقة عند حدوث أي خطأ شبكي دون إيقاف الخادم.
* **حماية الاستضافات المشتركة**: معالجة مضبوطة بالدفعات (افتراضياً: مهمتان في الدقيقة، بحد أقصى 5) لضمان عدم تجاوز الذاكرة أو المعالج.
* *التوثيق المفصل*: [docs/features.md](docs/features.md#6-asynchronous-background-queue--wp-cron-scheduling)

---

## مقارنة الخطة المجانية بالنسخة الاحترافية (Pro)

| الميزة / الإمكانية | الخطة المجانية (Free) | النسخة الاحترافية (Pro) | الحالة في الكود |
| :--- | :---: | :---: | :--- |
| **الحد الشهري لتوليد المقالات** | **6 مقالات / شهرياً** | **غير محدود** | مفعلة برمجياً (`FREE_MONTHLY_ARTICLE_LIMIT = 6`) |
| **الحد الأقصى للكلمات في المقال** | **700 كلمة** | **حتى 6,000 كلمة** | مفعلة برمجياً (`FREE_MAX_WORD_COUNT = 700`) |
| **المولد اليدوي المباشر** | متوفر | متوفر | متوفر |
| **الاتصال بمزودي الذكاء الاصطناعي** | متوفر | متوفر | Gemini, Groq, OpenRouter, Mistral, Cerebras, Ollama |
| **بيانات السيو الأساسية** | متوفر | متوفر | العنوان، والوصف، والكلمة المفتاحية |
| **محرك التقسيم الذكي الطويل** | مقيد (حتى 700ك) | **متوفر (حتى 6,000ك)** | مفعل |
| **التجميع المحلي في PHP** | متوفر | متوفر | متوفر |
| **الربط الداخلي التلقائي** | غير متوفر | **متوفر** | مقيد بالترخيص (`advanced_seo`) |
| **توليد FAQ JSON-LD Schema** | غير متوفر | **متوفر** | مقيد بالترخيص (`advanced_seo`) |
| **الأتمتة بالخلفية عبر WP-Cron** | غير متوفر | **متوفر** | مقيد بالترخيص (`automation`) |
| **قواعد الجدولة المتكررة** | غير متوفر | **متوفر** | مقيد بالترخيص (`automation`) |
| **طابور المهام والدفعات المتعددة** | غير متوفر | **متوفر** | مقيد بالترخيص (`bulk_generation`) |
| **السلاسل والدورات التعليمية** | غير متوفر | **متوفر** | مقيد بالترخيص (`series_courses`) |
| **التبديل التلقائي عند خطأ 429** | غير متوفر | **متوفر** | متوفر في Pro |
| **نقل الترخيص بين المواقع** | غير متاح | **متوفر بنقرة واحدة** | متوفر |
| **فترة السماح عند انقطاع الخادم** | غير متاح | **14 يوماً سماح** | مفعلة برمجياً (`GRACE_PERIOD_DURATION = 1209600`) |

*للاطلاع على الشروط والتفاصيل الكاملة، راجع [docs/editions.md](docs/editions.md).*

---

## التنصيب والتشغيل لأول مرة

### 1. الشراء والتحميل
احصل على مفتاح الترخيص وحمل حزمة التنصيب الرسمية من المتجر الرسمي:  
👉 **[https://code-elta6ur.net/shop/content-engine-pro](https://code-elta6ur.net/shop/content-engine-pro)**

### 2. التنصيب داخل ووردبريس
1. من لوحة تحكم ووردبريس، اذهب إلى: **إضافات** -> **أضف إضافة جديدة** -> **رفع إضافة**.
2. اختر الملف المضغوط `code-elta6ur-content-engine.zip` واضغط **التنصيب الآن**.
3. اضغط **تفعيل الإضافة**.

### 3. تفعيل الترخيص التجاري
1. انتقل إلى: **محرك المحتوى** -> **الترخيص التجاري** في القائمة الجانبية.
2. ألصق مفتاح الترخيص (`CEPRO-XXXX-XXXX-XXXX-XXXX`) واضغط **تفعيل الترخيص**.

### 4. إعداد أول مزود مجاني
1. اذهب إلى: **محرك المحتوى** -> **المزودون (AI)**.
2. ضع مفتاح الـ API المجاني لـ **Google Gemini** أو **Groq Cloud**.
3. اضغط **استكشاف النماذج** واختر النموذج، وفعل الحالة إلى **نشط**، ثم احفظ الإعدادات واضغط **فحص الاتصال**.

### 5. إنشاء أول مقال
1. اذهب إلى: **محرك المحتوى** -> **إنشاء مقال**.
2. حدد موضوع المقال، وعدد الكلمات، واللغة، واضغط **بدء توليد المقال الذكي**.

*لمزيد من التفاصيل، راجع [دليل التنصيب](docs/installation.md) و [دليل البدء السريع](docs/getting-started.md).*

---

## المتطلبات والتوافق البرمجي

| المتطلب | الحد الأدنى | الموصى به والمختبر |
| :--- | :--- | :--- |
| **ووردبريس** | **6.0** | **6.0 حتى 6.7** |
| **إصدار PHP** | **8.1** | **8.1 حتى 8.2+** *(تم الفحص على 8.2.33)* |
| **قاعدة البيانات** | MySQL 5.7+ / MariaDB 10.3+ | MySQL 8.0+ / MariaDB 10.6+ |
| **ملحقات PHP المطلوبة** | `curl`، `json`، `mbstring`، `openssl`، `pcre` | قياسية في كافة الاستضافات الحديثة |

*للاطلاع على المتطلبات الكاملة، راجع [docs/requirements.md](docs/requirements.md).*

---

## دليل التوثيق الشامل

| الملف | المحتوى والغرض |
| :--- | :--- |
| [**نظرة عامة على المنتج (Overview)**](docs/overview.md) | شرح المعمارية، وفلسفة التصميم، والحلول التحريرية. |
| [**دليل المميزات الكامل (Features)**](docs/features.md) | فهرس تفصيلي لكافة الميزات المبرمجة والمدعومة. |
| [**التراخيص والإصدارات (Editions)**](docs/editions.md) | مقارنة الخطة المجانية بالنسخة المدفوعة وفترة السماح. |
| [**متطلبات النظام (Requirements)**](docs/requirements.md) | المتطلبات البرمجية وملحقات PHP وقواعد البيانات. |
| [**دليل التنصيب (Installation)**](docs/installation.md) | طرق التثبيت والترقية والحذف الآمن. |
| [**دليل البداية السريعة (Getting Started)**](docs/getting-started.md) | خطوات الإعداد خطوة بخطوة من المفتاح وحتى أول مقال. |
| [**دليل المستخدم ولوحة التحكم (User Guide)**](docs/user-guide.md) | شرح كامل لكل شاشة وخيار داخل ووردبريس. |
| [**مرجع الإعدادات المتقدمة (Configuration)**](docs/configuration.md) | هيكلية خيارات قاعدة البيانات وخطافات الفلاتر للمطورين. |
| [**دليل التوافق والتكامل (Integrations)**](docs/integrations.md) | التكامل مع إضافات السيو ونقاط نهاية المزودين وجوتنبرج. |
| [**مخططات سير العمل (Workflows)**](docs/workflows.md) | تدفقات البيانات التحريرية وآليات إعادة المحاولة. |
| [**دليل استكشاف الأخطاء (Troubleshooting)**](docs/troubleshooting.md) | رسائل الخطأ وحلولها والتعامل مع حدود الاستخدام. |
| [**الأمان والخصوصية (Security & Privacy)**](docs/security-and-privacy.md) | شفافية حركة البيانات، وحجب الأسرار، وتأمين النماذج. |
| [**سياسة الاستخدام والتراخيص (Licensing & Usage)**](docs/licensing-and-usage.md) | نموذج المنتج التجاري ونقل المفاتيح والامتثال القانوني. |
| [**الأسئلة الشائعة (FAQ)**](docs/faq.md) | إجابات وافية عن الأسئلة التقنية والتجارية المتكررة. |
| [**خارطة الطريق (Roadmap)**](docs/roadmap.md) | التطويرات المستقبلية وآلية تقييم طلبات الميزات. |
| [**الدعم الفني والتشخيص (Support)**](docs/support.md) | قنوات الدعم المعتمدة وقواعد الخصوصية وجمع التشخيصات. |
| [**ملاحظات المطورين (Development Notes)**](docs/development-notes.md) | أنماط التصميم ومنظومة الاختبارات الآلية (328 فحصاً). |

---

## البلاغات وتتبع المشكلات (Issues)

نعتمد نظام GitHub Issues كقناة رسمية عامة لتتبع بلاغات الأخطاء، واقتراحات التحسين، ومناقشات المطورين:

* 📋 [**استعراض كافة البلاغات (View All Issues)**](https://github.com/mokashreef/Content-Engine-Pro/issues): تصفح سجل المشكلات المفتوحة والمناقشات الفنية الجارية.
* 🐛 [**تقديم بلاغ عن مشكلة برمجية (Submit Bug Report)**](https://github.com/mokashreef/Content-Engine-Pro/issues/new?template=bug_report.yml): تعبئة نموذج البلاغ المنظم مع تحديد إصدار ووردبريس ومزود الـ AI وسجلات النظام بعد حجب المفاتيح.
* 💡 [**اقتراح ميزة أو تحسين جديد (Feature Request)**](https://github.com/mokashreef/Content-Engine-Pro/issues/new?template=feature_request.yml): تقديم فكرة جديدة تفيد ناشري ومطوري ووردبريس.
* 🔒 **الدعم الفني والفوترة الخاصة**: لأي استفسارات تتعلق بمفاتيح التراخيص، أو نقل النطاقات، أو الفوترة، تواصل مباشرة عبر [support@code-elta6ur.net](mailto:support@code-elta6ur.net).

---

## طلبات الدمج والمساهمة (Pull Requests)

نرحب بالمساهمات المجتمعية في تدقيق وتطوير التوثيق، وتحسين الترجمة العربية، وتصحيح الأخطاء المعتمدة:

* 🔀 [**استعراض طلبات الدمج (View Pull Requests)**](https://github.com/mokashreef/Content-Engine-Pro/pulls): متابعة التحسينات والمساهمات قيد المراجعة.
* 🚀 [**إنشاء طلب دمج جديد (Open a Pull Request)**](https://github.com/mokashreef/Content-Engine-Pro/compare): إرسال تحسيناتك البرمجية أو التوثيقية الموجهة للفرع الرئيسي `main`.
* 📖 [**دليل المساهمة (Contributing Guidelines)**](CONTRIBUTING.md): مراجعة ضوابط المساهمة ومعايير الأمان قبل إرسال طلب الدمج.
* 🛡️ [**ميثاق السلوك (Code of Conduct)**](CODE_OF_CONDUCT.md): معايير التعامل المهني والاحترام المتبادل داخل مجتمع المشروع.

---

## الشراء والاشتراك التجاري

أطلق العنان لقوة الأتمتة التحريرية الكاملة في موقعك مع **Content Engine Pro**:

* **توليد مقالات غير محدود شهرياً**
* **تقسيم ذكي متقدم للمقالات الطويلة حتى 6,000 كلمة**
* **أتمتة كاملة وجدولة بالخلفية عبر WP-Cron كل دقيقة**
* **ربط داخلي ذكي وتوليد تلقائي لبيانات FAQ Schema**
* **سلاسل ودورات تعليمية متسلسلة معرفياً**
* **تبديل تلقائي للمزودين عند تجاوز حدود الاستخدام (429 Failover)**
* **فترة سماح 14 يوماً لضمان استقرار العمل عند أي انقطاع**

👉 **[شراء ترخيص Content Engine Pro — فقط 10 دولارات سنوياً](https://code-elta6ur.net/shop/content-engine-pro)**  
*تسليم فوري للمفتاح • ترخيص لموقع واحد مع مرونة النقل الكاملة • أمان وحفظ دائم لبياناتك*

---

## بيان الملكية وحقوق الاستخدام

حقوق النشر والتوزيع (c) 2026 محمد أبو خشريف / كود التطور (Code Elta6ur). كافة الحقوق محفوظة.

محرك **Content Engine Pro** هو منتج تجاري مملوك لشركة **Code Elta6ur** ([https://code-elta6ur.net/](https://code-elta6ur.net/)). 

يلتزم الكود المصدري البرمجي الذي يتعامل مباشرة مع واجهات برمجة ووردبريس بمتطلبات رخصة جنو العمومية (GPLv2 or later) للتوافق التام مع بيئة ووردبريس. وتظل العلامات التجارية، والخدمات السحابية لخادم التراخيص، والتحديثات الرسمية، والدعم الفني المباشر خاضعة للشروط التجارية لشركة Code Elta6ur.

للتفاصيل الكاملة، يرجى مراجعة [PROPRIETARY-NOTICE.md](PROPRIETARY-NOTICE.md) و [docs/licensing-and-usage.md](docs/licensing-and-usage.md).
