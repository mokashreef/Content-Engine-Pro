# Frequently Asked Questions (FAQ) — Content Engine Pro

Here are answers to common technical and commercial questions regarding **Content Engine Pro**.

---

## General & Architecture

### What is Content Engine Pro?
Content Engine Pro is an autonomous WordPress plugin developed by Mohammad Abu Khashreef under [Code Elta6ur](https://code-elta6ur.net/). It automates the complete editorial workflow of researching, writing, structuring, internal linking, schema formatting, and scheduling articles and courses directly inside your WordPress site.

### How does it differ from asking ChatGPT or Claude directly in a browser?
Asking a web chatbot gives you unformatted, isolated text that cuts off around 800–1,200 words. You still have to outline, format headings, copy into WordPress, convert to Gutenberg blocks, research internal links, write meta descriptions, generate FAQ schema, and schedule dates manually. Content Engine Pro automates that entire remaining 85% of the publishing workflow inside WordPress.

### Does Content Engine Pro require expensive monthly SaaS subscriptions?
**No.** You can run Content Engine Pro completely free of AI API charges using generous free-tier quotas from Google Gemini, Groq Cloud, OpenRouter, and Cerebras, or run 100% free private inference via local models in Ollama.

---

## Capabilities & Workflows

### Can Content Engine Pro generate comprehensive articles over 3,000 or 5,000 words?
**Yes.** The built-in sequential chunking engine calculates optimal section outlines (up to 12 sections) and writes each chunk iteratively with context memory summaries, supporting comprehensive articles up to 6,000 words without server timeouts.

### Does assembling long articles waste AI token quota?
**No.** Content Engine Pro uses **Zero-Token Local Assembly** in server-side PHP (`CTA_CE_Chunker::assemble_locally`). PHP joins the sections, checks H2/H3 heading hierarchy, injects internal links, and converts markup into native WordPress Gutenberg blocks without consuming AI credits.

### How does the automated internal linking work?
The engine queries up to 50 of your published WordPress posts and tags, identifies natural keyword matches in newly generated paragraphs, and injects contextual hyperlinks without breaking existing anchors or headings.

### Which SEO plugins are supported?
Content Engine Pro automatically detects **Yoast SEO**, **Rank Math**, and **All in One SEO (AIOSEO)**, populating their title, description, and focus keyword fields. If no dedicated SEO plugin is installed, it injects native `<meta>` tags and validated JSON-LD FAQ Schema directly into `wp_head`.

### Does Content Engine Pro support Arabic and RTL languages?
**Yes.** The plugin features native Arabic editorial prompts and includes dedicated RTL stylesheets (`admin-rtl.css`) for a seamless experience on Arabic WordPress sites.

---

## Commercial Licensing & Purchasing

### How much does Content Engine Pro cost?
Content Engine Pro is available for an annual subscription of **$10 USD / Year** per active site.

### Where can I purchase a commercial license?
You can purchase directly from the official store:  
[https://code-elta6ur.net/shop/content-engine-pro](https://code-elta6ur.net/shop/content-engine-pro)

### Can I move my license from one domain to another?
**Yes.** You can click **Deactivate License** in your WordPress dashboard (`Content Engine -> License`) at any time and immediately activate your key on a new domain or staging site.

### What happens if my commercial license expires?
We enforce a strict **Zero Data Lock-in** policy:
* **All existing posts and drafts remain 100% yours forever** in `wp_posts`.
* Custom database tables and settings are preserved.
* Background cron scheduling pauses gracefully until renewal.
* Manual generation defaults to the Free Plan limits (6 articles per month, up to 700 words).

### What happens if the licensing server is temporarily unreachable?
Content Engine Pro includes a built-in **14-Day Offline Grace Period** (`GRACE_PERIOD_DURATION = 1209600`). If your web host experiences network issues or the licensing server is undergoing maintenance, all Pro automations, schedules, and queues continue running seamlessly for 14 days.

### Can I run Content Engine Pro on local development environments (e.g., LocalWP, XAMPP)?
**Yes.** The plugin functions normally on local hosts, connecting to cloud AI providers or self-hosted local models running in Ollama on `localhost:11434`.
