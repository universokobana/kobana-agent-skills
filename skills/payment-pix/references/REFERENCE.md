# Kobana Payment - Complete Reference

Complete documentation for paying Pix charges, decoding QR codes, and managing payment batches. Covers both MCP tools and REST API.

---

## MCP Server Reference

**npm package**: [kobana-mcp-payment](https://www.npmjs.com/package/kobana-mcp-payment)

### Quick Start

```bash
# Local mode (stdio)
KOBANA_ACCESS_TOKEN=your_token npx kobana-mcp-payment

# HTTP mode (hosted)
KOBANA_ACCESS_TOKEN=your_token npx kobana-mcp-payment-http
```

### Configuration

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `KOBANA_ACCESS_TOKEN` | Yes | - | Bearer access token for Kobana API |
| `KOBANA_API_URL` | No | `https://api.kobana.com.br` | Base URL for Kobana API |

### All Available MCP Tools (20+ tools)

#### Pix Payments (5 tools)

| Tool | Description |
|------|-------------|
| `list_payment_pix` | List all Pix payments with filters |
| `create_payment_pix` | Create a new Pix payment |
| `get_payment_pix` | Get a specific Pix payment |
| `cancel_payment_pix` | Cancel a pending Pix payment |
| `decode_pix_emv` | Decode a Pix EMV (QR code/copy-paste) |

#### Payment Batches (5 tools)

| Tool | Description |
|------|-------------|
| `list_payment_batches` | List all payment batches |
| `create_payment_pix_batch` | Create a Pix payment batch |
| `get_payment_batch` | Get a specific batch |
| `approve_payment_batch` | Approve a batch |
| `reprove_payment_batch` | Reprove (cancel) a batch |

#### Bank Billets (4 tools)

| Tool | Description |
|------|-------------|
| `list_payment_billet` | List all billet payments |
| `create_payment_billet` | Create a billet payment |
| `get_payment_billet` | Get a specific billet payment |
| `cancel_payment_billet` | Cancel a pending billet payment |

#### Tax Payments (4 tools)

| Tool | Description |
|------|-------------|
| `list_payment_tax` | List all tax payments |
| `create_payment_tax` | Create a tax payment |
| `get_payment_tax` | Get a specific tax payment |
| `cancel_payment_tax` | Cancel a pending tax payment |

#### Financial Accounts (2 tools)

| Tool | Description |
|------|-------------|
| `list_financial_accounts` | List all financial accounts |
| `get_financial_account` | Get a specific financial account |

### MCP Tool Parameters

#### decode_pix_emv

**Required Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `emv` | string | The Pix EMV string (QR code copy-paste) |

**Response:**
```json
{
  "type": "dynamic",
  "key": "123e4567-e89b-12d3-...",
  "key_type": "random",
  "merchant_name": "EMPRESA LTDA",
  "merchant_city": "SAO PAULO",
  "amount": 50.00,
  "txid": "PAYMENT123456789",
  "description": "Invoice January 2026"
}
```

#### create_payment_pix

**Required Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `financial_account_uid` | string | UUID of source financial account |
| `amount` | decimal | Payment amount in BRL |

**For EMV Payment (Dynamic Pix):**

| Parameter | Type | Description |
|-----------|------|-------------|
| `emv` | string | Pix EMV string (from QR code) |

**For Key Payment (Static Pix):**

| Parameter | Type | Description |
|-----------|------|-------------|
| `type` | string | Must be `key` |
| `key_type` | string | `cpf`, `cnpj`, `email`, `phone`, `random` |
| `key` | string | The Pix key value |
| `beneficiary.document_number` | string | Beneficiary CPF/CNPJ |
| `beneficiary.name` | string | Beneficiary name |

**Optional Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `scheduled_to` | date | Schedule date (YYYY-MM-DD) |
| `description` | string | Payment description |
| `identifier` | string | Payment identifier (bank-specific) |
| `external_id` | string | External ID for tracking |
| `custom_data` | object | Custom metadata (JSON) |
| `tags` | array | Tags for organization |

#### create_payment_pix_batch

| Parameter | Type | Description |
|-----------|------|-------------|
| `financial_account_uid` | string | UUID of source financial account (required) |
| `payments` | array | Array of payment objects or UIDs (required) |

#### list_payment_pix

| Parameter | Type | Description |
|-----------|------|-------------|
| `status` | string | Filter by status |
| `registration_status` | string | Filter by registration status |
| `financial_account_uid` | string | Filter by financial account |
| `created_from` | string | Created date from (YYYY-MM-DD) |
| `created_to` | string | Created date to (YYYY-MM-DD) |
| `per_page` | integer | Results per page (default 50) |
| `page` | integer | Page number |

#### get_payment_pix / cancel_payment_pix

| Parameter | Type | Description |
|-----------|------|-------------|
| `uid` | string | Payment UID (required) |

#### approve_payment_batch / reprove_payment_batch

| Parameter | Type | Description |
|-----------|------|-------------|
| `uid` | string | Batch UID (required) |

### Client Configurations

#### Claude Desktop (macOS)

`~/Library/Application Support/Claude/claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "kobana-payment": {
      "command": "npx",
      "args": ["-y", "kobana-mcp-payment"],
      "env": {
        "KOBANA_ACCESS_TOKEN": "your_access_token"
      }
    }
  }
}
```

#### Claude Desktop (Windows)

`%APPDATA%\Claude\claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "kobana-payment": {
      "command": "npx",
      "args": ["-y", "kobana-mcp-payment"],
      "env": {
        "KOBANA_ACCESS_TOKEN": "your_access_token"
      }
    }
  }
}
```

#### Claude Code

`.mcp.json` in project root:

```json
{
  "mcpServers": {
    "kobana-payment": {
      "command": "npx",
      "args": ["-y", "kobana-mcp-payment"],
      "env": {
        "KOBANA_ACCESS_TOKEN": "your_access_token"
      }
    }
  }
}
```

#### Remote MCP (Hosted at mcp.kobana.com.br)

```json
{
  "mcpServers": {
    "kobana-payment": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote",
        "https://mcp.kobana.com.br/payment/mcp",
        "--header",
        "Authorization: Bearer your_access_token"
      ]
    }
  }
}
```

#### Remote MCP Sandbox

```json
{
  "mcpServers": {
    "kobana-payment": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote",
        "https://mcp-sandbox.kobana.com.br/payment/mcp",
        "--header",
        "Authorization: Bearer your_sandbox_token"
      ]
    }
  }
}
```

### OAuth Scopes

| Resource | Scope |
|----------|-------|
| Pix Payments | `payment.pix` |
| Bank Billets | `payment.billet` |
| Tax Payments | `payment.tax` |
| Payment Batches | `payment.batches` |
| Financial Accounts | `financial.accounts` |

---

## REST API Reference

### Base URLs

| Environment | URL |
|-------------|-----|
| Production | `https://api.kobana.com.br` |
| Sandbox | `https://api-sandbox.kobana.com.br` |

### Authentication

```
Authorization: Bearer {your_api_token}
```

### Common Headers

| Header | Required | Description |
|--------|----------|-------------|
| `Authorization` | Yes | Bearer token |
| `Content-Type` | Yes | `application/json` |
| `User-Agent` | No | Valid email for contact |
| `X-Idempotency-Key` | No | Prevents replay processing |

### Pix Decoding

#### Decode Pix EMV
```
POST /v2/payment/pix/decode
```

**Request Body:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `emv` | string | Yes | The Pix EMV string (copy-paste from QR code) |

**Success Response (200 OK):**
```json
{
  "status": 200,
  "data": {
    "type": "dynamic",
    "key": "123e4567-e89b-12d3-a456-426614174000",
    "key_type": "random",
    "merchant_name": "EMPRESA LTDA",
    "merchant_city": "SAO PAULO",
    "amount": 50.00,
    "txid": "PAYMENT123456789",
    "description": "Invoice January 2026",
    "due_date": "2026-02-10",
    "beneficiary": {
      "document_number": "12.345.678/0001-90",
      "name": "EMPRESA LTDA"
    }
  }
}
```

### Pix Payments

#### List Pix Payments
```
GET /v2/payment/pix
```

**Query Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `page` | integer | Page number |
| `per_page` | integer | Items per page (default 50, max 50) |
| `status` | string | Filter by status |
| `registration_status` | string | Filter by registration status |
| `financial_account_uid` | string | Filter by financial account |
| `created_from` | date | Created date from (YYYY-MM-DD) |
| `created_to` | date | Created date to |
| `scheduled_from` | date | Scheduled date from |
| `scheduled_to` | date | Scheduled date to |
| `external_id` | string | Filter by external ID |
| `tags` | string | Filter by tags (comma-separated) |

#### Create Pix Payment
```
POST /v2/payment/pix
```

**Required Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `financial_account_uid` | string | UUID of the source financial account |
| `amount` | decimal | Payment amount in BRL |

**Payment by EMV (Dynamic Pix):**
| Parameter | Type | Description |
|-----------|------|-------------|
| `emv` | string | Pix EMV string (from QR code) |

**Payment by Pix Key (Static Pix):**
| Parameter | Type | Description |
|-----------|------|-------------|
| `type` | string | Must be `key` |
| `key_type` | string | `cpf`, `cnpj`, `email`, `phone`, `random` |
| `key` | string | The Pix key value |
| `beneficiary.document_number` | string | Beneficiary CPF/CNPJ |
| `beneficiary.name` | string | Beneficiary name |

**Optional Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `scheduled_to` | date | Schedule date (YYYY-MM-DD) |
| `description` | string | Payment description |
| `identifier` | string | Payment identifier (bank-specific) |
| `external_id` | string | External ID for tracking |
| `custom_data` | object | Custom metadata (JSON) |
| `tags` | array | Tags for organization |

#### Get / Cancel Pix Payment
```
GET /v2/payment/pix/{uid}
PUT /v2/payment/pix/{uid}/cancel
```

### Payment Batches

#### List / Create / Get / Approve / Reprove
```
GET /v2/payment/batches
POST /v2/payment/pix_batches
GET /v2/payment/batches/{uid}
PUT /v2/payment/batches/{uid}/approve
PUT /v2/payment/batches/{uid}/reprove
```

**Create Batch Required Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `financial_account_uid` | string | UUID of the source financial account |
| `payments` | array | Array of payment objects or UIDs |

---

## Response Structure

### Pix Payment Response
```json
{
  "uid": "019c0cbe-f018-717e-be6e-...",
  "status": "pending",
  "registration_status": "pending",
  "financial_account_uid": "018df180-7208-727b-...",
  "amount": 150.00,
  "scheduled_to": "2026-02-01",
  "target": {
    "beneficiary": {
      "document_number": "12.345.678/0001-90",
      "name": "Company LTDA"
    },
    "pix": {
      "pix_type": "dynamic",
      "txid": "PAYMENT123456789",
      "key": "123e4567-e89b-12d3-...",
      "key_type": "random",
      "end_to_end_id": null
    },
    "payment_kind": "pix"
  },
  "description": "Payment for Invoice",
  "external_id": "payment_001",
  "created_at": "2026-01-30T11:32:45-03:00",
  "updated_at": "2026-01-30T11:32:45-03:00"
}
```

### Payment Batch Response
```json
{
  "uid": "019c0cbe-f018-717e-...",
  "status": "pending",
  "registration_status": "pending",
  "financial_account_uid": "018df180-7208-727b-...",
  "transport_kind": "pix",
  "payments": [
    {
      "uid": "019c0cbe-f018-717e-...",
      "status": "pending",
      "amount": 150.00,
      "target": {...}
    }
  ],
  "created_at": "2026-01-30T11:32:45-03:00",
  "updated_at": "2026-01-30T11:32:45-03:00"
}
```

### List Response (200 OK)
```json
{
  "status": 200,
  "data": [...],
  "pagination": {
    "count": 100,
    "page": 1,
    "items": 50,
    "pages": 2,
    "last": false,
    "next": "/v2/payment/pix?page=2",
    "prev": null
  }
}
```

---

## Status Reference

### Payment Status
| Status | Description |
|--------|-------------|
| `pending` | Pending - initial state |
| `awaiting_approval` | Awaiting approval (batch) |
| `approved` | Approved, processing |
| `confirmed` | Confirmed by bank |
| `rejected` | Rejected by bank |
| `reproved` | Reproved (canceled) |

### Registration Status
| Status | Description |
|--------|-------------|
| `pending` | Waiting for registration |
| `requested` | Sent to bank |
| `confirmed` | Confirmed at bank |
| `rejected` | Rejected by bank |
| `failed` | Registration failed |

### Batch Status
| Status | Description |
|--------|-------------|
| `pending` | Pending - initial state |
| `awaiting_approval` | Awaiting manual approval |
| `approved` | Approved, processing |
| `confirmed` | All payments confirmed |
| `reproved` | Batch reproved |

---

## Pix Types

| Type | Description |
|------|-------------|
| `static` | Static Pix - Fixed key, variable amount |
| `dynamic` | Dynamic Pix - Generated for specific charge |
| `dynamic_due_date` | Dynamic Pix with due date (Pix Cobrança) |

## Key Types

| Type | Description | Format |
|------|-------------|--------|
| `cpf` | Brazilian individual tax ID | `XXX.XXX.XXX-XX` or `XXXXXXXXXXX` |
| `cnpj` | Brazilian company tax ID | `XX.XXX.XXX/XXXX-XX` or `XXXXXXXXXXXXXX` |
| `email` | Email address | `user@example.com` |
| `phone` | Phone number with country code | `+5511999999999` |
| `random` | Random EVP key | UUID format |

---

## Error Responses

### 401 Unauthorized
```json
{
  "status": 401,
  "errors": [{"title": "Invalid API Token", "code": "unauthorized"}]
}
```

### 422 Validation Error
```json
{
  "status": 422,
  "errors": [{"code": "validation_error", "param": "emv", "detail": "Invalid EMV format"}]
}
```

---

## Webhooks

### Payment Events
| Event | Description |
|-------|-------------|
| `payment.db.created` | Payment created in database |
| `payment.pix.requested` | Payment sent to bank |
| `payment.pix.confirmed` | Payment confirmed at bank |
| `payment.pix.rejected` | Payment rejected by bank |
| `payment.pix.cancel.requested` | Cancellation requested |
| `payment.pix.cancel.confirmed` | Cancellation confirmed |

### Batch Events
| Event | Description |
|-------|-------------|
| `payment.batch.db.created` | Batch created in database |
| `payment.pix_batch.confirmed` | Batch confirmed at bank |
| `payment.batch.approve.requested` | Approval requested |
| `payment.batch.approve.confirmed` | Approval confirmed |
| `payment.batch.reprove.requested` | Reproval requested |
| `payment.batch.reprove.confirmed` | Reproval confirmed |

---

## Additional Resources

- **GitHub Repository**: https://github.com/universokobana/kobana-mcp-servers
- **npm Package**: https://www.npmjs.com/package/kobana-mcp-payment
- **API Documentation**: https://developers.kobana.com.br
- **API Specification**: https://github.com/universokobana/kobana-api-specs
- **Central Bank Pix Documentation**: https://www.bcb.gov.br/estabilidadefinanceira/pix
