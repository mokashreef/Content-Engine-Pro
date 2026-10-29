# Content Engine Pro — Licensing Server API Specification & Contract
**Brand**: `code elta6ur`  
**Product**: Content Engine Pro  
**Version**: 2.0.0  
**Author**: Mohammad Abu Khashreef ([https://mohammad.code-elta6ur.com](https://mohammad.code-elta6ur.com))

---

## 1. Architectural Overview & Separation of Concerns

The licensing architecture cleanly decouples payment gateways from plugin runtime execution:

```
[Payment Gateway]  (Stripe / Paddle / LemonSqueezy / WooCommerce)
       │
       ▼
   [Orders]        (Order ID, Customer Email, Amount Paid, Currency)
       │
       ▼
   [Licenses]      (License Key: CEPRO-XXXX-XXXX-XXXX-XXXX, Plan, Expires At)
       │
       ▼
  [Activations]    (Domain, Site URL, Installation UUID, Activated At, Token)
```

The plugin interacts solely with the **Licensing Layer** via the authoritative endpoints defined below. Payment providers can be switched or augmented in the future without altering a single line of plugin code.

---

## 2. Global Protocol & Authentication

- **Base URL**: `https://mohammad.code-elta6ur.com`
- **Protocol**: HTTPS / TLS 1.3
- **Format**: JSON (`Content-Type: application/json`, `Accept: application/json`)
- **Required Headers**:
  - `X-CTA-Installation`: Stable site installation UUID (e.g., `4a59f51e-61c0-44dc-9d41-33ea06b9dc31`).
  - `X-CTA-Signature`: HMAC-SHA256 signature computed over the raw JSON request body using the installation UUID as secret key.
  - `X-CTA-Product`: `ContentEnginePro`
  - `X-CTA-Product-Version`: `2.0.0`
- **Rate Limiting**: 60 requests per minute per installation UUID. HTTP 429 returned if exceeded.

---

## 3. Endpoints Specification

### 3.1. Activate License
- **Route**: `POST /api/v1/license/activate`
- **Purpose**: Validates license key and binds the active WordPress installation to the license.

#### Request Body
```json
{
  "license_key": "CEPRO-ABCD-1234-EFGH-5678",
  "domain": "example.com",
  "site_url": "https://example.com",
  "site_uuid": "4a59f51e-61c0-44dc-9d41-33ea06b9dc31",
  "version": "2.0.0",
  "action": "activate"
}
```

#### Success Response (`200 OK`)
```json
{
  "success": true,
  "status": "active",
  "plan": "pro_annual",
  "activation_token": "act_9f81a7b6c5d4e3f201",
  "activated_at": "2026-09-21 16:00:00",
  "expires_at": "2027-09-21 16:00:00",
  "domain": "example.com",
  "message": "License activated successfully."
}
```

#### Error Response (`400 / 403 / 409`)
```json
{
  "success": false,
  "status": "activation_limit_reached",
  "message": "Activation limit reached. Please deactivate from your other website first.",
  "error_code": "LIMIT_REACHED"
}
```

---

### 3.2. Validate / Periodic Sync
- **Route**: `POST /api/v1/license/validate`
- **Purpose**: Periodic background check (every 7 days) to verify that the subscription is active, renewed, or revoked.

#### Request Body
```json
{
  "license_key": "CEPRO-ABCD-1234-EFGH-5678",
  "activation_token": "act_9f81a7b6c5d4e3f201",
  "domain": "example.com",
  "site_uuid": "4a59f51e-61c0-44dc-9d41-33ea06b9dc31",
  "action": "validate"
}
```

#### Success Response (`200 OK`)
```json
{
  "success": true,
  "status": "active",
  "expires_at": "2027-09-21 16:00:00",
  "message": "License is active."
}
```

---

### 3.3. Deactivate License
- **Route**: `POST /api/v1/license/deactivate`
- **Purpose**: Frees up the domain activation slot, allowing the customer to move their Pro license to another site.

#### Request Body
```json
{
  "license_key": "CEPRO-ABCD-1234-EFGH-5678",
  "activation_token": "act_9f81a7b6c5d4e3f201",
  "domain": "example.com",
  "site_uuid": "4a59f51e-61c0-44dc-9d41-33ea06b9dc31",
  "action": "deactivate"
}
```

#### Success Response (`200 OK`)
```json
{
  "success": true,
  "status": "not_activated",
  "message": "License deactivated successfully."
}
```

---

### 3.4. Renew License (Portal Redirect or Direct Webhook)
- **Route**: `POST /api/v1/license/renew`
- **Purpose**: Triggered by payment renewal webhook to extend `expires_at` by 1 calendar year (365 days).

---

## 4. Standard Status Codes & Client Behavior

| Status Code | Meaning | Client Behavior |
| :--- | :--- | :--- |
| `active` | Valid Pro subscription | Full Pro features enabled (Automation, Scheduling, Bulk, Courses). |
| `not_activated` | No key entered | Pro features gated; customer prompted to activate. |
| `expired` | 1-year access expired | Automation pauses gracefully; all customer data remains intact; renewal notice shown. |
| `invalid` | Key does not exist | User notified with clear Arabic/English validation error. |
| `activation_limit_reached` | 1/1 site used | Customer instructed to deactivate previous site. |
| `revoked` | License cancelled/refunded | Gated with support link. |
| `suspended` | Payment dispute / review | Temporarily paused until resolved. |
| `connection_error` | Server offline / timeout | **14-day Grace Period active**; Pro features continue running seamlessly. |

---

## 5. Security & Anti-Tampering Rules

1. **HMAC Signature**: The client plugin validates and computes HMAC-SHA256 signatures for local state storage using WordPress `wp_salt('auth')`. Manual edits to database values are rejected.
2. **Key Masking**: Server and client mask license keys (`CEPRO-****-****-****-5678`) in all log entries and public UI.
3. **No Sensitive Telemetry**: Neither prompts, generated articles, nor customer database credentials are ever transmitted to the licensing server.
