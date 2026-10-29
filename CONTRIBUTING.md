# Contributing Guidelines — Content Engine Pro

Thank you for your interest in **Content Engine Pro**, developed by Mohammad Abu Khashreef under **Code Elta6ur**.

Official Product Page: [https://code-elta6ur.net/shop/content-engine-pro](https://code-elta6ur.net/shop/content-engine-pro)

---

## 1. Commercial & Repository Scope

Content Engine Pro is a commercial, proprietary WordPress product. This GitHub repository serves as the official public documentation hub, architecture reference, and public issue tracker.

Because this is a closed-source commercial software product:
* We **welcome** documentation improvements, typo corrections, translation contributions, and technical bug reports.
* We **do not accept** unsolicited architectural refactors or unauthorized redistributions of the proprietary plugin code.
* If you wish to propose an architectural change or integration, please open a [Feature Request](https://github.com/mokashreef/Content-Engine-Pro/issues/new?template=feature_request.yml) first for discussion.

---

## 2. Reporting Issues & Bugs

Before opening an issue on GitHub:
1. Search the [existing issues](https://github.com/mokashreef/Content-Engine-Pro/issues) to ensure the topic has not already been reported.
2. Check the [Troubleshooting Guide](docs/troubleshooting.md) and [FAQ](docs/faq.md) for known resolutions.
3. Use the official [Bug Report Template](.github/ISSUE_TEMPLATE/bug_report.yml).

### Strict Confidentiality Rule:
> [!CAUTION]
> **NEVER paste sensitive credentials into public issues:**
> * Do NOT include API keys, license keys (`CEPRO-...`), database passwords, or private domain authentication tokens.
> * Always redact logs before pasting.

---

## 3. Documentation Improvements

If you find a typo, broken relative link, or unclear explanation in our documentation:
1. Fork the repository.
2. Create a focused topic branch (`git checkout -b docs/fix-navigation-links`).
3. Commit your changes with a clear message following [Conventional Commits](https://www.conventionalcommits.org/) (e.g., `docs: clarify WP-Cron setup instructions`).
4. Submit a Pull Request targeting the `main` branch.

---

## 4. Coding & Quality Standards

When submitting documentation or allowable code patches:
* Follow standard WordPress Coding Standards (WPCS).
* Ensure all Markdown conforms to standard GitHub Flavored Markdown (GFM).
* Validate all relative markdown links against existing repository files.

