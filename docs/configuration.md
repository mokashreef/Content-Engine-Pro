# Configuration Reference — Content Engine Pro

This document provides a technical reference for all configuration options, database settings, developer filters, and environment controls in **Content Engine Pro**.

---

## 1. WordPress Options Schema

Content Engine Pro persists its operational settings in the standard WordPress `wp_options` table:

### `cta_ce_general_settings` (Array)
Controls global publishing behaviors:

```php
[
    'default_language'        => 'ar',             // 'ar' (Arabic) or 'en' (English)
    'default_status'          => 'draft',          // 'draft' (recommended) or 'publish'
    'batch_limit'             => 2,                // Jobs processed per cron minute (min: 1, max: 5)
    'max_retries'             => 3,                // Maximum retry attempts before marking failed
    'retry_delay_1'           => 5,                // Delay before 1st retry in minutes
    'retry_delay_2'           => 15,               // Delay before 2nd retry in minutes
    'retry_delay_3'           => 30,               // Delay before 3rd retry in minutes
    'auto_internal_linking'   => 1,                // 1 = enabled, 0 = disabled
    'max_internal_links'      => 5,                // Max internal links injected per article (0 to 10)
    'auto_tags'               => 1,                // 1 = assign AI tags automatically
    'auto_category'           => 1,                // 1 = assign category automatically
    'enable_faq_schema'       => 1,                // 1 = generate JSON-LD FAQ schema
    'research_mode_default'   => 'writing_only',   // 'writing_only' or 'research_and_write'
    'enable_image_generation' => 0,                // Placeholder for future media integrations
    'delete_on_uninstall'     => 0,                // 1 = wipe custom DB tables on plugin deletion
]
```

---

### `cta_ce_providers_config` (Array)
Stores connection credentials, model preferences, and endpoints for each provider:

```php
[
    'gemini' => [
        'name'        => 'Google Gemini',
        'tier_type'   => 'free_tier',
        'api_url'     => 'https://generativelanguage.googleapis.com/v1beta/models/',
        'api_key'     => 'AIzaSy...',
        'model'       => 'gemini-1.5-flash',
        'max_tokens'  => 8192,
        'temperature' => 0.7,
        'timeout'     => 60,
        'status'      => 'active',                 // 'active' or 'disabled'
    ],
    'groq' => [
        'name'        => 'Groq Cloud',
        'tier_type'   => 'free_tier',
        'api_url'     => 'https://api.groq.com/openai/v1/chat/completions',
        'api_key'     => 'gsk_...',
        'model'       => 'llama-3.3-70b-versatile',
        'max_tokens'  => 8000,
        'temperature' => 0.7,
        'timeout'     => 60,
        'status'      => 'active',
    ],
    // OpenRouter, Mistral, Cerebras, and Custom configurations follow the same schema
]
```

---

### `cta_ce_provider_priority` (Array)
An ordered list of provider keys establishing the automatic fallback hierarchy:

```php
[ 'gemini', 'groq', 'openrouter', 'mistral', 'cerebras', 'custom' ]
```

---

### `cta_ce_license_data` (Array)
Maintains commercial license state, domain binding, and anti-tamper signature:

```php
[
    'license_key'      => 'CEPRO-XXXX-XXXX-XXXX-XXXX',
    'status'           => 'active',                // active, expired, connection_error, etc.
    'plan'             => 'pro_annual',
    'activation_token' => 'act_...',
    'activated_at'     => '2026-09-21 16:00:00',
    'expires_at'       => '2027-09-21 16:00:00',
    'activated_domain' => 'example.com',
    'site_uuid'        => '4a59f51e-61c0-44dc-9d41-33ea06b9dc31',
    'last_checked_at'  => '2026-10-29 08:00:00',
    'grace_until'      => null,                    // Set to UTC timestamp during offline grace
    'tamper_hash'      => 'a8f1b...',              // HMAC-SHA256 hash using wp_salt('auth')
]
```

---

## 2. Developer Action & Filter Hooks

Developers can customize Content Engine Pro's behavior using WordPress filter hooks:

### Register Custom AI Providers
Add your own proprietary or local inference providers:

```php
add_filter( 'cta_ce_registered_providers', function( array $providers ): array {
    // Class must implement CTA_CE_Provider_Interface
    $providers['my_llm'] = new My_Custom_Enterprise_Provider();
    return $providers;
} );
```

---

### Modify Feature Permissions
Programmatically adjust feature authorization logic:

```php
add_filter( 'cta_ce_license_can', function( bool $authorized, string $feature, array $license_data ): bool {
    // Example: Grant specific feature access on local staging environments
    if ( defined( 'WP_ENVIRONMENT_TYPE' ) && 'staging' === WP_ENVIRONMENT_TYPE ) {
        return true;
    }
    return $authorized;
}, 10, 3 );
```

---

### Override Licensing Server Endpoint
Useful for local staging or corporate intranet testing:

```php
add_filter( 'cta_ce_license_server_url', function( string $default_url ): string {
    return 'https://code-elta6ur.net';
} );
```

---

## 3. Secure Credential Handling

Content Engine Pro enforces strict security measures for sensitive configuration data:

* **Masking in User Interfaces**: All API keys and license keys are masked when rendered in HTML fields (e.g., `AIzaSy...cdef`, `CEPRO-****-****-****-5678`).
* **Scrubbing in Error Logs**: The logging subsystem (`CTA_CE_Security::sanitize_log_message`) strips authorization tokens, Bearer headers, and query parameters before writing to `wp_cta_ce_logs`.
* **Anti-Tampering Verification**: The local license state contains an HMAC-SHA256 signature calculated with `wp_salt('auth')`. Direct unauthorized manipulation of database rows invalidates the license cache and forces an online re-check.

---

## 4. Settings Export and Import

To migrate configurations across environments:

1. Navigate to **Content Engine** -> **Settings**.
2. Scroll to the **Export & Import** section.
3. Click **Export Settings** to generate a clean JSON export containing your general settings, provider priorities, and prompt templates.
4. On your destination site, upload the JSON file and click **Import Settings** to restore configuration.
