---
name: charge-pix
description: Guide for managing Pix charges, accounts, and payments using Kobana. Use when the user wants to create, list, update, or cancel Pix charges, manage Pix accounts, or handle charge payments. Supports both MCP tools (preferred) and REST API.
license: Complete terms in LICENSE.txt
metadata:
  author: Kobana
  version: "3.0"
---

# Kobana Charge Pix Skill

Manage Pix charges, accounts, and payments via Kobana.

## How It Works

This skill supports two modes of operation:

1. **MCP (preferred)** — If the `kobana-mcp-charge` MCP server is available, use MCP tools directly. This is simpler and recommended.
2. **REST API (fallback)** — If MCP is not available, use HTTP calls to the Kobana API.

> **Rule:** Always try MCP tools first. Only fall back to the REST API if MCP tools are not available or not configured.

---

## MCP Mode

### Available MCP Tools

#### Pix Accounts
| Tool | Description |
|------|-------------|
| `list_charge_pix_accounts` | List all Pix accounts |
| `create_charge_pix_account` | Create a new Pix account |
| `get_charge_pix_account` | Get a specific Pix account |

#### Pix Charges
| Tool | Description |
|------|-------------|
| `list_charge_pix` | List all Pix charges with filters |
| `create_charge_pix` | Create a new Pix charge |
| `get_charge_pix` | Get a specific Pix charge |
| `update_charge_pix` | Update a Pix charge |
| `cancel_charge_pix` | Cancel a Pix charge |

### Creating a Pix Charge (MCP)

#### Step 1: Get the Pix Account UID

```
Use tool: list_charge_pix_accounts
```

#### Step 2: Create the Pix Charge

**Instant Pix (e-commerce, delivery):**
```json
{
  "amount": 150.00,
  "pix_account_uid": "018df180-7208-727b-...",
  "expire_at": "2026-01-31T12:30:00-03:00",
  "registration_kind": "instant",
  "payer": {
    "document_number": "111.321.322-09",
    "name": "João da Silva"
  },
  "external_id": "order_12345"
}
```

**Billing Pix (invoices, subscriptions):**
```json
{
  "amount": 500.00,
  "pix_account_uid": "018df180-7208-727b-...",
  "expire_at": "2026-02-10T23:59:59-03:00",
  "registration_kind": "billing",
  "payer": {
    "document_number": "12.345.678/0001-90",
    "name": "Company LTDA"
  },
  "fine_type": "percentage",
  "fine_percentage": 2.0,
  "interest_type": "monthly_percentage_calendar",
  "interest_percentage": 1.0,
  "revoke_days": 30
}
```

#### Step 3: Get the QR Code

The initial response may not contain the QR Code (asynchronous registration). Poll using `get_charge_pix` until `registration_status` is `confirmed`.

```
Use tool: get_charge_pix
Parameters: { "uid": "019c0cbe-f018-717e-..." }
```

### MCP Server Setup

Add to `.mcp.json` in your project root (Claude Code) or `~/Library/Application Support/Claude/claude_desktop_config.json` (Claude Desktop macOS):

```json
{
  "mcpServers": {
    "kobana-charge": {
      "command": "npx",
      "args": ["-y", "kobana-mcp-charge"],
      "env": {
        "KOBANA_ACCESS_TOKEN": "your_access_token"
      }
    }
  }
}
```

**Sandbox:**
```json
{
  "mcpServers": {
    "kobana-charge": {
      "command": "npx",
      "args": ["-y", "kobana-mcp-charge"],
      "env": {
        "KOBANA_ACCESS_TOKEN": "your_sandbox_token",
        "KOBANA_API_URL": "https://api-sandbox.kobana.com.br"
      }
    }
  }
}
```

**Remote MCP (Hosted):**
```json
{
  "mcpServers": {
    "kobana-charge": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote",
        "https://mcp.kobana.com.br/charge/mcp",
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

#### Pix Accounts
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v2/charge/pix_accounts` | List all Pix accounts |
| POST | `/v2/charge/pix_accounts` | Create a new Pix account |
| GET | `/v2/charge/pix_accounts/{uid}` | Get a specific Pix account |
| PUT | `/v2/charge/pix_accounts/{uid}` | Update a Pix account |
| DELETE | `/v2/charge/pix_accounts/{uid}` | Delete a Pix account |

#### Pix Charges
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v2/charge/pix` | List all Pix charges |
| POST | `/v2/charge/pix` | Create a new Pix charge |
| GET | `/v2/charge/pix/{uid}` | Get a specific Pix charge |
| PUT | `/v2/charge/pix/{uid}/update` | Update a Pix charge |
| DELETE | `/v2/charge/pix/{uid}` | Delete a Pix charge |
| POST | `/v2/charge/pix/{uid}/cancel` | Cancel a Pix charge |

#### Pix Commands
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v2/charge/pix/{pix_uid}/commands` | List commands for a Pix charge |
| GET | `/v2/charge/pix/{pix_uid}/commands/{id}` | Get a specific command |

#### Charge Payments
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v2/charge/payments` | List all payments |
| POST | `/v2/charge/payments` | Create a new payment |
| GET | `/v2/charge/payments/{uid}` | Get a specific payment |
| DELETE | `/v2/charge/payments/{uid}` | Delete a payment |

### Creating a Pix Charge (API)

**Minimum Required:**
```json
POST /v2/charge/pix

{
  "amount": 100.50,
  "pix_account_uid": "018df180-7208-727b-...",
  "expire_at": "2026-12-02T10:03:56-03:00"
}
```

**Complete Example (Billing Pix):**
```json
POST /v2/charge/pix

{
  "amount": 500.00,
  "pix_account_uid": "018df180-7208-727b-...",
  "expire_at": "2026-02-10T23:59:59-03:00",
  "registration_kind": "billing",
  "payer": {
    "document_number": "12.345.678/0001-90",
    "name": "Company LTDA",
    "email": "finance@company.com"
  },
  "fine_type": "percentage",
  "fine_percentage": 2.0,
  "interest_type": "monthly_percentage_calendar",
  "interest_percentage": 1.0,
  "discount_type": "advance_percentage_calendar",
  "discount_first_percentage": 5.0,
  "discount_first_days": 5,
  "revoke_days": 30,
  "external_id": "invoice_2026_001",
  "tags": ["monthly", "subscription"],
  "message": "Invoice January/2026"
}
```

### Important: Asynchronous Behavior

The QR Code is **NOT** returned in the initial response. Options:

1. **Poll the charge** - GET `/v2/charge/pix/{uid}` until `registration_status` is `confirmed`
2. **Use webhooks** - Configure webhook for `pix.register.confirmed` event

### Listing and Filtering

```
GET /v2/charge/pix?status=opened&created_from=2026-01-01&per_page=50
```

| Parameter | Description |
|-----------|-------------|
| `status` | Filter by status (opened, paid, overdue, canceled) |
| `registration_status` | Filter by registration status |
| `pix_account_uid` | Filter by Pix account |
| `created_from` / `created_to` | Filter by creation date |
| `expire_from` / `expire_to` | Filter by expiration date |
| `paid_from` / `paid_to` | Filter by payment date |
| `txid` | Filter by TXID |
| `external_id` | Filter by external ID |
| `tags` | Filter by tags |
| `page` / `per_page` | Pagination |

---

## Common Parameters

### Required
| Parameter | Type | Description |
|-----------|------|-------------|
| `amount` | decimal | Value in BRL (minimum 0.01) |
| `pix_account_uid` | string | UUID of the Pix Account |
| `expire_at` | string | Expiration date (ISO 8601) |

### Payer Data
| Parameter | Type | Description |
|-----------|------|-------------|
| `payer.document_number` | string | CPF or CNPJ |
| `payer.name` | string | Full name or company name |
| `payer.email` | string | Email (optional) |

### Fine and Interest (Billing Pix only)
| Parameter | Type | Description |
|-----------|------|-------------|
| `fine_type` | string | `percentage` or `amount` |
| `fine_percentage` | decimal | Fine percentage |
| `interest_type` | string | See interest types in reference |
| `interest_percentage` | decimal | Interest percentage |
| `revoke_days` | integer | Days active after due date (required with fine/interest) |

### Tracking
| Parameter | Type | Description |
|-----------|------|-------------|
| `external_id` | string | External ID for tracking |
| `custom_data` | object | Custom metadata (JSON) |
| `tags` | array | Tags for organization |
| `message` | string | Message to payer (max 140 chars) |

## Pix Types

### Instant Pix (`registration_kind: "instant"`)
- Short expiration (minutes to hours)
- No interest, fines, or discounts
- Use for: e-commerce, delivery, point-of-sale

### Billing Pix (`registration_kind: "billing"`)
- Formal due date
- Supports interest, fines, discounts
- Stays active after due date (if `revoke_days` set)
- Use for: monthly invoices, subscriptions

## Status Reference

### Charge Status
- `opened` - Open (initial)
- `paid` - Paid
- `overdue` - Overdue
- `canceled` - Canceled
- `generation_failed` - Registration failed

### Registration Status
- `pending` - Waiting
- `confirmed` - Ready (has QR Code)
- `rejected` / `failed` - Error

## Best Practices

1. **Prefer MCP when available** - Simpler and more reliable
2. **Poll or use webhooks** - QR Code comes asynchronously
3. **Use external_id** - Track charges in your system
4. **Set revoke_days** - Required for interest/fines
5. **Validate CPF/CNPJ** - Before creating the charge
6. **Use X-Idempotency-Key header** - Prevent duplicates (API mode)
7. **Test in sandbox** - Before production

## Reference Documentation

See [references/REFERENCE.md](references/REFERENCE.md) for complete documentation including all parameters, response formats, error codes, MCP tools, and API endpoints.
