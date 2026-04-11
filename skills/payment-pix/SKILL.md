---
name: payment-pix
description: Guide for paying Pix charges using Kobana. Use when the user wants to pay Pix invoices, decode QR codes, or manage Pix payments from financial accounts. Supports both MCP tools (preferred) and REST API.
license: Complete terms in LICENSE
metadata:
  author: Kobana
  version: "1.1.0"
---

# Kobana Payment Pix Skill

Pay Pix charges, decode QR codes, and manage Pix payments via Kobana.

## How It Works

This skill supports two modes of operation:

1. **MCP (preferred)** — If the `kobana-mcp-payment` MCP server is available, use MCP tools directly. This is simpler and recommended.
2. **REST API (fallback)** — If MCP is not available, use HTTP calls to the Kobana API.

> **Rule:** Always try MCP tools first. Only fall back to the REST API if MCP tools are not available or not configured.

---

## MCP Mode

### Available MCP Tools

#### Pix Payments
| Tool | Description |
|------|-------------|
| `list_payment_pix` | List all Pix payments |
| `create_payment_pix` | Create a new Pix payment |
| `get_payment_pix` | Get a specific Pix payment |
| `cancel_payment_pix` | Cancel a pending Pix payment |

#### Pix Decoding
| Tool | Description |
|------|-------------|
| `decode_pix_emv` | Decode a Pix EMV (QR code/copy-paste) |

#### Payment Batches
| Tool | Description |
|------|-------------|
| `list_payment_batches` | List all payment batches |
| `create_payment_pix_batch` | Create a Pix payment batch |
| `get_payment_batch` | Get a specific batch |
| `approve_payment_batch` | Approve a batch |
| `reprove_payment_batch` | Reprove (cancel) a batch |

#### Financial Accounts
| Tool | Description |
|------|-------------|
| `list_financial_accounts` | List all financial accounts |
| `get_financial_account` | Get a specific financial account |

### Paying a Pix Charge (MCP)

#### Step 1: Get the Financial Account UID

```
Use tool: list_financial_accounts
```

#### Step 2: Decode the Pix EMV (Optional)

Before paying, decode the QR code to validate the charge data:

```
Use tool: decode_pix_emv
Parameters: {
  "emv": "00020126580014br.gov.bcb.pix0136123e4567-e89b-12d3-a456-426614174000..."
}
```

#### Step 3: Create the Payment

**Payment by EMV (Dynamic Pix):**
```json
{
  "financial_account_uid": "018df180-7208-727b-...",
  "emv": "00020126580014br.gov.bcb.pix0136123e4567-e89b-12d3-a456-426614174000...",
  "amount": 150.00,
  "external_id": "payment_001"
}
```

**Payment by Pix Key (Static Pix):**
```json
{
  "financial_account_uid": "018df180-7208-727b-...",
  "type": "key",
  "key_type": "email",
  "key": "recipient@example.com",
  "amount": 150.00,
  "beneficiary": {
    "document_number": "12.345.678/0001-90",
    "name": "Company LTDA"
  },
  "external_id": "payment_002"
}
```

#### Step 4: Create a Batch to Send

After creating payments, create a batch to send them to the bank:

```
Use tool: create_payment_pix_batch
Parameters: {
  "financial_account_uid": "018df180-7208-727b-...",
  "payments": [
    { "uid": "019c0cbe-f018-717e-..." }
  ]
}
```

#### Step 5: Check Batch Status

```
Use tool: get_payment_batch
Parameters: { "uid": "019c0cbe-f018-717e-..." }
```

If the batch is `awaiting_approval`, approve it:
```
Use tool: approve_payment_batch
Parameters: { "uid": "019c0cbe-f018-717e-..." }
```

### MCP Server Setup

#### Claude Code

If you installed the `kobana-agent-skills` plugin, the MCP servers are already configured. Just set the environment variable before running Claude Code:

```bash
export KOBANA_ACCESS_TOKEN="your_access_token"
claude
```

For sandbox, also set:
```bash
export KOBANA_API_URL="https://api-sandbox.kobana.com.br"
```

Or add to `.mcp.json` in your project root:

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

#### Claude Desktop

Add to your config file:
- **macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Windows**: `%APPDATA%\Claude\claude_desktop_config.json`

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

> **Important:** Replace `your_access_token` with your actual Kobana API token. Claude Desktop does not support environment variable expansion (`${VAR}`) — you must paste the real token value directly.

For sandbox, add `"KOBANA_API_URL": "https://api-sandbox.kobana.com.br"` to the `env` object.

#### Remote MCP (Hosted)

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

---

## API Mode (Fallback)

### Base URLs

```
Production: https://api.kobana.com.br
Sandbox:    https://api-sandbox.kobana.com.br
```

### Authentication

```
Authorization: Bearer {your_api_token}
```

### API Endpoints Overview

#### Pix Payments
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v2/payment/pix` | List all Pix payments |
| POST | `/v2/payment/pix` | Create a new Pix payment |
| GET | `/v2/payment/pix/{uid}` | Get a specific Pix payment |
| PUT | `/v2/payment/pix/{uid}/cancel` | Cancel a Pix payment |

#### Pix Decoding
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/v2/payment/pix/decode` | Decode a Pix EMV (QR code/copy-paste) |

#### Payment Batches
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v2/payment/batches` | List all payment batches |
| POST | `/v2/payment/pix_batches` | Create a Pix payment batch |
| GET | `/v2/payment/batches/{uid}` | Get a specific batch |
| PUT | `/v2/payment/batches/{uid}/approve` | Approve a batch |
| PUT | `/v2/payment/batches/{uid}/reprove` | Reprove a batch |

### Paying a Pix Charge (API)

#### Step 1: Decode the Pix EMV (Optional but Recommended)

```json
POST /v2/payment/pix/decode

{
  "emv": "00020126580014br.gov.bcb.pix0136123e4567-e89b-12d3-a456-426614174000..."
}
```

#### Step 2: Create the Payment

**By EMV (QR Code/Copy-Paste):**
```json
POST /v2/payment/pix

{
  "financial_account_uid": "018df180-7208-727b-...",
  "emv": "00020126580014br.gov.bcb.pix0136123e4567-e89b-12d3-a456-426614174000...",
  "amount": 150.00,
  "scheduled_to": "2026-01-31",
  "external_id": "payment_001"
}
```

**By Pix Key (Static Pix):**
```json
POST /v2/payment/pix

{
  "financial_account_uid": "018df180-7208-727b-...",
  "type": "key",
  "key_type": "email",
  "key": "recipient@example.com",
  "amount": 150.00,
  "beneficiary": {
    "document_number": "12.345.678/0001-90",
    "name": "Company LTDA"
  },
  "external_id": "payment_001"
}
```

### Important: Two-Step Flow

Pix payments require a two-step process:

1. **Create Payment** - `POST /v2/payment/pix` creates a pending payment
2. **Create Batch** - `POST /v2/payment/pix_batches` sends payments to the bank

**Creating a Batch:**
```json
POST /v2/payment/pix_batches

{
  "financial_account_uid": "018df180-7208-727b-...",
  "payments": [
    { "uid": "019c0cbe-f018-717e-..." }
  ]
}
```

### Batch Approval Workflow

Some financial accounts require manual approval:

1. **Batch created** - Status: `pending` or `awaiting_approval`
2. **Approve** - `PUT /v2/payment/batches/{uid}/approve`
3. **Or Reprove** - `PUT /v2/payment/batches/{uid}/reprove`

### Listing and Filtering

```
GET /v2/payment/pix?status=pending&per_page=50
```

| Parameter | Description |
|-----------|-------------|
| `status` | Filter by status (pending, confirmed, rejected) |
| `registration_status` | Filter by registration status |
| `financial_account_uid` | Filter by financial account |
| `created_from` / `created_to` | Filter by creation date |
| `page` / `per_page` | Pagination |

---

## Common Parameters

### Required
| Parameter | Type | Description |
|-----------|------|-------------|
| `financial_account_uid` | string | UUID of source financial account |
| `amount` | decimal | Payment amount in BRL |

### For EMV Payment (Dynamic Pix)
| Parameter | Type | Description |
|-----------|------|-------------|
| `emv` | string | Pix EMV string (from QR code) |

### For Key Payment (Static Pix)
| Parameter | Type | Description |
|-----------|------|-------------|
| `type` | string | Must be `key` |
| `key_type` | string | `cpf`, `cnpj`, `email`, `phone`, `random` |
| `key` | string | The Pix key value |

### Beneficiary
| Parameter | Type | Description |
|-----------|------|-------------|
| `beneficiary.document_number` | string | CPF or CNPJ |
| `beneficiary.name` | string | Full name or company name |

### Optional
| Parameter | Type | Description |
|-----------|------|-------------|
| `scheduled_to` | date | Schedule date (YYYY-MM-DD) |
| `description` | string | Payment description |
| `external_id` | string | External ID for tracking |
| `custom_data` | object | Custom metadata (JSON) |
| `tags` | array | Tags for organization |

## Payment Types

### By EMV (Dynamic Pix)
- Use when you have a QR code or copy-paste string
- Amount may be fixed by the charge
- Most common for invoices and bills

### By Pix Key (Static Pix)
- Use when paying directly to a Pix key
- You define the amount
- Use for donations, tips, or manual payments

## Key Types

| Type | Description | Example |
|------|-------------|---------|
| `cpf` | CPF number | `111.222.333-44` |
| `cnpj` | CNPJ number | `11.222.333/0001-44` |
| `email` | Email address | `user@example.com` |
| `phone` | Phone number | `+5511999999999` |
| `random` | Random key (EVP) | `123e4567-e89b-12d3-...` |

## Status Reference

### Payment Status
- `pending` - Pending (initial)
- `awaiting_approval` - Awaiting approval
- `approved` - Approved
- `confirmed` - Confirmed by bank
- `rejected` - Rejected by bank
- `reproved` - Reproved (canceled before sending)

### Registration Status
- `pending` - Waiting for registration
- `requested` - Sent to bank
- `confirmed` - Confirmed at bank
- `rejected` / `failed` - Error

### Batch Status
- `pending` - Pending (initial)
- `awaiting_approval` - Awaiting approval
- `approved` - Approved
- `confirmed` - All payments confirmed
- `reproved` - Reproved

## Best Practices

1. **Prefer MCP when available** - Simpler and more reliable
2. **Decode before paying** - Validate the charge data before creating payment
3. **Use batches for multiple payments** - More efficient than individual payments
4. **Set external_id** - Track payments in your system
5. **Schedule payments** - Use `scheduled_to` for future-dated payments
6. **Use X-Idempotency-Key header** - Prevent duplicates (API mode)
7. **Test in sandbox** - Before production

## Reference Documentation

See [references/REFERENCE.md](references/REFERENCE.md) for complete documentation including all parameters, response formats, error codes, MCP tools, and API endpoints.
