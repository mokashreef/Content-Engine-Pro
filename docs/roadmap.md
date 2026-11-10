# Product Roadmap & Feature Evolution — Content Engine Pro

This document outlines active development priorities and planned architectural enhancements for **Content Engine Pro**.

Official Product Page: [https://code-elta6ur.net/shop/content-engine-pro](https://code-elta6ur.net/shop/content-engine-pro)

---

## 1. Delivered Reliability Milestones (`v2.0.1` – `v2.0.4`)

Recent maintenance and architectural updates delivered prior to feature expansion include:

* **Queue Resiliency & Stale Recovery**: Time-budgeted 40-second checkpoint continuation across sequential article sections, automatic recovery of stuck processing items (`recover_stale_processing_items(4)`), single-item retry fixes, and 2-minute transient locks.
* **Schedule Precision & Topic Bank**: Strict `normalize_time_slot()` alignment, explicit `next_run` tracking, and ordered Topic Bank dequeuing to replace generic placeholder titles.
* **AI Provider Hardening**: Gemini `thought`-part filtering, explicit `finishReason` surfacing, 90-second default timeout, 6-second single-provider 429 backoff, and locked-provider re-selection when disabled.
* **Native Gutenberg Block Integrity**: Mixed-HTML depth normalization (`normalize_existing_blocks()`), tag/URL/code token masking (`clean_artifacts()`), trailing fragment repair, and Gutenberg comment-safe persistence (`sanitize_gutenberg_content()` / `insert_gutenberg_post()`).

---

## 2. Verified Architecture in Progress (`v2.1+`)

The following features reflect underlying code structures already prepared in version 2.0.4:

### A. Automated AI Featured Image Generation
* **Current State**: Setting toggle `enable_image_generation` exists in `cta_ce_general_settings`, currently disabled to ensure production stability.
* **Objective**: Connect selected image generation endpoints (such as open-weight image models or custom APIs) to automatically generate and assign relevant WordPress featured images and inline figures with localized `alt` text.

### B. Deep Web Research Pipeline (`research_and_write`)
* **Current State**: The research subsystem (`class-cta-ce-research-service.php`) supports a basic research mode toggle in settings (`writing_only` vs. `research_and_write`).
* **Objective**: Expand live web scraping and SERP research capabilities to dynamically extract current statistical citations and verify factual claims before compiling the article outline.

### C. Enhanced Multi-Site & Network Management
* **Objective**: Streamlined management for WordPress Multisite (WPMU) installations, allowing network administrators to manage API provider priorities globally across sub-sites.

---

## 2. Feature Request Evaluation Process

As a commercial product, Content Engine Pro evolves based on verified feedback from active publishers, agencies, and site operators:

1. **Submit a Request**: Open a [Feature Request on GitHub](https://github.com/mokashreef/Content-Engine-Pro/issues/new?template=feature_request.yml) detailing your publishing workflow, expected behavior, and practical impact.
2. **Architectural Review**: Requests are assessed for hosting efficiency, WordPress standard compliance, and broad utility across the user base.
3. **Staged Implementation**: Approved features undergo rigorous unit and integration testing in the QA test harness before deployment into production releases.

> [!NOTE]
> We intentionally avoid publishing arbitrary release dates. Enhancements are released only after comprehensive automated testing across all 8 test suites confirms zero regressions.

