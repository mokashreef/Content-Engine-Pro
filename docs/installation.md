# Installation & Deployment Guide — Content Engine Pro

This guide walks you through installing, activating, and updating **Content Engine Pro** on your WordPress website.

Official Product & Downloads: [https://code-elta6ur.net/shop/content-engine-pro](https://code-elta6ur.net/shop/content-engine-pro)

---

## 1. Supported Installation Methods

### Method A: WordPress Admin Dashboard Upload (Recommended)

1. Obtain your official plugin package (`code-elta6ur-content-engine.zip`) from your purchase confirmation email or from the [Code Elta6ur Customer Portal](https://code-elta6ur.net/).
2. Log in to your WordPress admin dashboard (`/wp-admin`).
3. Navigate to **Plugins** -> **Add New Plugin**.
4. Click the **Upload Plugin** button at the top of the screen.
5. Click **Choose File**, select `code-elta6ur-content-engine.zip`, and click **Install Now**.
6. Once the installation completes, click **Activate Plugin**.

---

### Method B: Manual SFTP / Hosting File Manager Upload

If your hosting provider restricts ZIP uploads via the WordPress dashboard:

1. Unzip `code-elta6ur-content-engine.zip` on your computer to produce the `code-elta6ur-content-engine` folder.
2. Connect to your server using SFTP or your hosting control panel's File Manager.
3. Upload the `code-elta6ur-content-engine` folder into the `wp-content/plugins/` directory of your WordPress installation.
4. Verify the folder structure on the server:
   ```
   wp-content/plugins/code-elta6ur-content-engine/
   ├── code-elta6ur-content-engine.php
   ├── admin/
   ├── includes/
   ├── providers/
   └── ...
   ```
5. In your WordPress admin, go to **Plugins** -> **Installed Plugins**, find **Content Engine Pro by code elta6ur**, and click **Activate**.

---

### Method C: WP-CLI Installation

For developers and system administrators:

```bash
# Navigate to your WordPress root directory
cd /var/www/html

# Install and activate the plugin package
wp plugin install /path/to/code-elta6ur-content-engine.zip --activate
```

---

## 2. What Happens During Plugin Activation

When Content Engine Pro activates, the activator service (`CTA_CE_Activator::activate`) automatically handles:

1. **Database Schema Creation**:
   * Uses WordPress `dbDelta()` to safely create 4 custom tables:
     * `wp_cta_ce_queue` (Task queue and state preservation)
     * `wp_cta_ce_schedules` (Recurring automation rules)
     * `wp_cta_ce_logs` (Sanitized event logs)
     * `wp_cta_ce_history` (Revision snapshots)
2. **Default Configuration Initialization**:
   * Installs default provider configuration entries (Gemini, Groq, OpenRouter, Mistral, Cerebras, Custom).
   * Sets default provider priority order.
   * Loads pre-configured, production-tested prompt templates (General Article, Technical Tutorial, Course Lesson).
3. **Cron Scheduling**:
   * Schedules the minutely background event (`cta_ce_minutely_event`).
4. **Admin UI Registration**:
   * Registers the **Content Engine** ("محرك المحتوى") menu and submenus in the WordPress sidebar.

---

## 3. Activating Your Pro License

To unlock unlimited generations, background cron automation, courses, and long-form chunking:

1. Go to **Content Engine** -> **License** ("الترخيص التجاري") in your WordPress sidebar.
2. Enter your commercial license key (e.g., `CEPRO-ABCD-1234-EFGH-5678`).
3. Click **Activate License**.
4. The system validates the key against the licensing server, generates an anti-tamper signature, and activates all Pro features immediately.

---

## 4. How to Update Content Engine Pro

When a new version is released:

1. Download the updated `code-elta6ur-content-engine.zip` package from your [Code Elta6ur Account](https://code-elta6ur.net/).
2. In WordPress admin, go to **Plugins** -> **Add New Plugin** -> **Upload Plugin**.
3. Select the new ZIP file and click **Install Now**.
4. WordPress will notify you that the plugin is already installed and display the version comparison. Click **Replace current with uploaded**.
5. The built-in migration handler (`CTA_CE_Activator::maybe_upgrade`) automatically detects the version difference, verifies database tables, and applies any schema updates without disrupting your existing posts, schedules, or configuration.

---

## 5. Deactivation and Uninstallation

* **Standard Deactivation**:
  * Temporarily unhooks the minutely cron event and admin menus.
  * **All customer data, generated posts, queue history, and settings remain 100% intact**.
* **Uninstallation (`uninstall.php`)**:
  * By default, uninstalling the plugin preserves your custom database tables and options so you never lose data accidentally.
  * If you wish to completely wipe all plugin data upon uninstallation, navigate to **Content Engine** -> **Settings** and enable **"Delete all data on uninstall"** before deleting the plugin.

