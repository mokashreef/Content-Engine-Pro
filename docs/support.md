# Customer Support & Diagnostics — Content Engine Pro

This document details verified customer support channels, diagnostic reporting procedures, and confidentiality guidelines for **Content Engine Pro**.

---

## 1. Verified Support Channels

Content Engine Pro is supported directly by the engineering team at **Code Elta6ur**:

* **Commercial Customer Support**: [support@code-elta6ur.net](mailto:support@code-elta6ur.net)
* **Official Software Portal**: [https://code-elta6ur.net/](https://code-elta6ur.net/)
* **Official Product Page**: [https://code-elta6ur.net/shop/content-engine-pro](https://code-elta6ur.net/shop/content-engine-pro)
* **Lead Architect & Developer**: Mohammad Abu Khashreef ([https://mohammad.code-elta6ur.com/](https://mohammad.code-elta6ur.com/))
* **Public GitHub Issues**: [GitHub Issue Tracker](https://github.com/mokashreef/Content-Engine-Pro/issues) *(For public bug reports, documentation updates, and technical feature requests only)*.

---

## 2. Public Feedback vs. Private Customer Support

| Channel | Appropriate For | Confidentiality |
| :--- | :--- | :--- |
| **Private Email Support**<br>(`support@code-elta6ur.net`) | Billing inquiries, license slot resets, private server debugging, enterprise inquiries. | **Private & Confidential** |
| **Public GitHub Issues** | Reproducible software bugs, UI rendering issues, documentation corrections, feature ideas. | **Publicly Visible** |

---

## 3. Gathering Diagnostic Information

To help us resolve your issue quickly, please collect diagnostic details from your WordPress admin before submitting a ticket:

1. **System Environment**:
   * Navigate to **Content Engine** -> **About & Diagnostics** ("حول المحرك").
   * Note your **WordPress Version**, **PHP Version**, **MySQL Version**, and **Memory Limit**.
2. **Relevant Log Entries**:
   * Navigate to **Content Engine** -> **Operation Logs** ("سجل العمليات").
   * Locate the timestamp of the failed operation and note the **Action**, **Status Code**, and **Error Message**.
   * *(Note: Content Engine Pro automatically redacts Bearer tokens and API keys in all logs)*.
3. **Queue Status (if applicable)**:
   * Navigate to **Content Engine** -> **Task Queue** ("طابور المهام") and check if the affected job shows an error message or attempt count.

---

## 4. Mandatory Confidentiality Warning

> [!CAUTION]
> **NEVER post sensitive credentials publicly on GitHub Issues or community forums:**
> * Do **NOT** post your commercial license key (`CEPRO-XXXX-XXXX-XXXX-XXXX`).
> * Do **NOT** post your AI provider API keys (`AIzaSy...`, `gsk_...`, etc.).
> * Do **NOT** post WordPress administrator passwords or database credentials.
> * Do **NOT** post private staging URLs containing access tokens.
> 
> Any GitHub issue containing exposed credentials will be immediately edited and redacted to protect your site.

