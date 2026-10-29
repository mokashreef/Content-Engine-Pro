# System Requirements & Compatibility — Content Engine Pro

This document specifies the technical prerequisites, PHP extensions, and server configurations required to run **Content Engine Pro** smoothly.

---

## 1. Version Compatibility Matrix

| Component | Minimum Declared | Tested & Recommended | Notes |
| :--- | :--- | :--- | :--- |
| **WordPress** | **6.0** | **6.0 – 6.7** | Declared in plugin headers (`code-elta6ur-content-engine.php` and `readme.txt`). Compatible with block themes, classic themes, and Gutenberg. |
| **PHP** | **8.1** | **8.1 – 8.2+** | Uses modern PHP features including typed properties, union types, and named arguments. Empirically tested against PHP 8.2.33. |
| **Database** | MySQL 5.7+ / MariaDB 10.3+ | MySQL 8.0+ / MariaDB 10.6+ | Utilizes `utf8mb4` charset and standard `dbDelta` schema definitions. |
| **Web Server** | Apache 2.4+ / Nginx 1.18+ / LiteSpeed | Apache / Nginx / LiteSpeed | Compatible with standard PHP-FPM and shared hosting configurations. |

> [!NOTE]
> Content Engine Pro is designed to run efficiently on standard shared hosting environments. The background queue processes jobs in controlled batches (default: 2 jobs per minute) to ensure memory consumption stays well within standard hosting limits.

---

## 2. Required PHP Extensions

Ensure the following PHP extensions are compiled and active on your server:

1. **`curl`**: Required by WordPress HTTP APIs (`wp_remote_post()`, `wp_remote_get()`) to communicate with AI endpoints and the licensing server.
2. **`json`**: Required for parsing structured JSON completions, FAQ schemas, prompt templates, and settings export/import.
3. **`mbstring`**: Required for multibyte string manipulation, character boundaries, and accurate Arabic UTF-8 word counting (`mb_strlen`, `mb_substr`).
4. **`openssl`**: Required for secure HTTPS/TLS 1.3 handshakes and local anti-tampering verification using `hash_hmac()` with SHA-256.
5. **`pcre`**: Required for regular expression matching in the internal linking engine and automated secret redaction in logs.

---

## 3. WordPress Configuration & Cron Architecture

### WP-Cron Recommendation:
Content Engine Pro schedules background tasks using a dedicated minutely interval (`cta_ce_every_minute`).

* **Default Shared Hosting**: Operates out of the box using standard WordPress visitor-triggered WP-Cron.
* **High-Traffic or Precision Automation (Recommended)**: For precise minute-by-minute execution on commercial websites, disable default WP-Cron in `wp-config.php` and configure a system-level crontab:

```php
// In wp-config.php:
define( 'DISABLE_WP_CRON', true );
```

```bash
# In your server crontab (runs every minute):
* * * * * wget -q -O - https://example.com/wp-cron.php?doing_wp_cron >/dev/null 2>&1
```

---

## 4. Outbound Network & Firewall Requirements

Your web server must be permitted to establish outbound HTTPS connections on **Port 443** to the endpoints you choose to activate:

| Service | Target Hostname | Purpose |
| :--- | :--- | :--- |
| **Licensing Server** | `https://code-elta6ur.net` | License activation, periodic validation (every 7 days), and deactivation. |
| **Google Gemini** | `https://generativelanguage.googleapis.com` | Google AI Studio Gemini API completions. |
| **Groq Cloud** | `https://api.groq.com` | Groq high-speed Llama completions. |
| **OpenRouter** | `https://openrouter.ai` | OpenRouter multi-model completions. |
| **Mistral AI** | `https://api.mistral.ai` | Mistral AI API completions. |
| **Cerebras Cloud** | `https://api.cerebras.ai` | Cerebras fast inference API. |
| **Local LLM (Optional)** | `http://localhost:11434` (or custom host/port) | Self-hosted Ollama or LM Studio instance. |

---

## 5. User Permissions & Role Capabilities

* **Configuration & Licensing**: Requires the WordPress `manage_options` capability (Administrators).
* **Manual Article Generation**: Accessible to Administrators and Editors with `edit_posts` permissions.
