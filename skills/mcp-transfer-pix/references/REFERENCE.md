# kobana-mcp-transfer - Complete Reference

MCP (Model Context Protocol) server for the Kobana Transfer API v2. This server provides tools for managing Pix transfers, TED transfers, internal transfers, and financial accounts.

**npm package**: [kobana-mcp-transfer](https://www.npmjs.com/package/kobana-mcp-transfer)

## Quick Start

```bash
# Local mode (stdio)
KOBANA_ACCESS_TOKEN=your_token npx kobana-mcp-transfer

# HTTP mode (hosted)
KOBANA_ACCESS_TOKEN=your_token npx kobana-mcp-transfer-http
```

## Configuration

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `KOBANA_ACCESS_TOKEN` | Yes | - | Bearer access token for Kobana API |
| `KOBANA_API_URL` | No | `https://api.kobana.com.br` | Base URL for Kobana API |

### Sandbox Environment

```bash
export KOBANA_API_URL=https://api-sandbox.kobana.com.br
export KOBANA_ACCESS_TOKEN=your_sandbox_token
npx kobana-mcp-transfer
```

## All Available Tools (18 tools)

### Pix Transfers (4 tools)

| Tool | Description |
|------|-------------|
| `list_transfer_pix` | List all Pix transfers with filters |
| `create_transfer_pix` | Create a new Pix transfer |
| `get_transfer_pix` | Get a specific Pix transfer |
| `cancel_transfer_pix` | Cancel a pending Pix transfer |

### TED Transfers (4 tools)

| Tool | Description |
|------|-------------|
| `list_transfer_ted` | List all TED transfers |
| `create_transfer_ted` | Create a new TED transfer |
| `get_transfer_ted` | Get a specific TED transfer |
| `cancel_transfer_ted` | Cancel a pending TED transfer |

### Internal Transfers (3 tools)

| Tool | Description |
|------|-------------|
| `list_transfer_internal` | List internal transfers |
| `create_transfer_internal_batch` | Create internal transfer batch |
| `get_transfer_internal` | Get a specific internal transfer |

### Transfer Batches (5 tools)

| Tool | Description |
|------|-------------|
| `list_transfer_batches` | List all transfer batches |
| `create_transfer_pix_batch` | Create a Pix transfer batch |
| `get_transfer_batch` | Get a specific batch |
| `approve_transfer_batch` | Approve a batch |
| `reprove_transfer_batch` | Reprove (cancel) a batch |

### Financial Accounts (2 tools)

| Tool | Description |
|------|-------------|
| `list_financial_accounts` | List all financial accounts |
| `get_financial_account` | Get a specific financial account |

## Tool Parameters

### create_transfer_pix

**Required Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `amount` | decimal | Transfer amount in BRL |
| `financial_account_uid` | string | UUID of source financial account |
| `type` | string | `key` or `bank_account` |

**For `type: "key"`:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `key_type` | string | `cpf`, `cnpj`, `email`, `phone`, `random` |
| `key` | string | The Pix key value |

**For `type: "bank_account"`:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `bank_account.compe_number` | integer | Bank COMPE code |
| `bank_account.ispb_number` | integer | Bank ISPB code (optional) |
| `bank_account.agency_number` | string | Agency number |
| `bank_account.agency_digit` | string | Agency digit (optional) |
| `bank_account.account_number` | string | Account number |
| `bank_account.account_digit` | string | Account digit |
| `bank_account.document_number` | string | Beneficiary CPF/CNPJ |

**Optional Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `scheduled_to` | date | Schedule date (YYYY-MM-DD) |
| `transfer_purpose` | string | Purpose code (default: "98") |
| `beneficiary.document_number` | string | Beneficiary CPF/CNPJ |
| `beneficiary.name` | string | Beneficiary name |
| `identifier` | string | Payment identifier (bank-specific) |
| `external_id` | string | External ID for tracking |
| `custom_data` | object | Custom metadata (JSON) |
| `tags` | array | Tags for organization |

### create_transfer_pix_batch

| Parameter | Type | Description |
|-----------|------|-------------|
| `financial_account_uid` | string | UUID of source financial account (required) |
| `transfers` | array | Array of transfer objects or UIDs (required) |

### list_transfer_pix

| Parameter | Type | Description |
|-----------|------|-------------|
| `status` | string | Filter by status |
| `registration_status` | string | Filter by registration status |
| `financial_account_uid` | string | Filter by financial account |
| `per_page` | integer | Results per page (default 50) |
| `page` | integer | Page number |

### get_transfer_pix

| Parameter | Type | Description |
|-----------|------|-------------|
| `uid` | string | Transfer UID (required) |

### cancel_transfer_pix

| Parameter | Type | Description |
|-----------|------|-------------|
| `uid` | string | Transfer UID (required) |

### approve_transfer_batch / reprove_transfer_batch

| Parameter | Type | Description |
|-----------|------|-------------|
| `uid` | string | Batch UID (required) |

## Key Types

| Type | Description | Example |
|------|-------------|---------|
| `cpf` | Brazilian individual tax ID | `111.222.333-44` |
| `cnpj` | Brazilian company tax ID | `11.222.333/0001-44` |
| `email` | Email address | `user@example.com` |
| `phone` | Phone with country code | `+5511999999999` |
| `random` | Random EVP key | UUID format |

## Transfer Purpose Codes

| Code | Description |
|------|-------------|
| `20` | Supplier Payment |
| `30` | Salary Payment |
| `32` | Fees Payment |
| `90` | Benefits Payment |
| `98` | Miscellaneous Payments (default) |

## Response Structure

### Pix Transfer Response

```json
{
  "uid": "019c0cbe-f018-717e-be6e-...",
  "status": "pending",
  "registration_status": "pending",
  "financial_account_uid": "018df180-7208-727b-...",
  "amount": 100.50,
  "scheduled_to": "2026-02-01",
  "target": {
    "beneficiary": {
      "document_number": "12.345.678/0001-90",
      "name": "Company LTDA"
    },
    "bank_account": null,
    "internal": null,
    "pix": {
      "pix_type": "key",
      "txid": null,
      "key": "recipient@example.com",
      "key_type": "email",
      "end_to_end_id": null
    },
    "transfer_kind": "pix"
  },
  "source": null,
  "confirmed_at": null,
  "rejected_error": null,
  "rejected_at": null,
  "transaction_code": null,
  "transfer_purpose": {
    "code": "98",
    "description": "Pagamentos Diversos"
  },
  "identifier": null,
  "external_id": "payment_001",
  "custom_data": null,
  "tags": [],
  "created_at": "2026-01-30T11:32:45-03:00",
  "updated_at": "2026-01-30T11:32:45-03:00"
}
```

### Transfer Batch Response

```json
{
  "uid": "019c0cbe-f018-717e-...",
  "status": "pending",
  "registration_status": "pending",
  "financial_account_uid": "018df180-7208-727b-...",
  "transport_kind": "pix",
  "transfers": [
    {
      "uid": "019c0cbe-f018-717e-...",
      "status": "pending",
      "amount": 100.50,
      "target": {...}
    }
  ],
  "created_at": "2026-01-30T11:32:45-03:00",
  "updated_at": "2026-01-30T11:32:45-03:00"
}
```

## Status Reference

### Transfer Status

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
| `confirmed` | All transfers confirmed |
| `reproved` | Batch reproved |

## Client Configurations

### Claude Desktop (macOS)

`~/Library/Application Support/Claude/claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "kobana-transfer": {
      "command": "npx",
      "args": ["-y", "kobana-mcp-transfer"],
      "env": {
        "KOBANA_ACCESS_TOKEN": "your_access_token"
      }
    }
  }
}
```

### Claude Desktop (Windows)

`%APPDATA%\Claude\claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "kobana-transfer": {
      "command": "npx",
      "args": ["-y", "kobana-mcp-transfer"],
      "env": {
        "KOBANA_ACCESS_TOKEN": "your_access_token"
      }
    }
  }
}
```

### Claude Code

`.mcp.json` in project root:

```json
{
  "mcpServers": {
    "kobana-transfer": {
      "command": "npx",
      "args": ["-y", "kobana-mcp-transfer"],
      "env": {
        "KOBANA_ACCESS_TOKEN": "your_access_token"
      }
    }
  }
}
```

### Remote MCP (Hosted at mcp.kobana.com.br)

```json
{
  "mcpServers": {
    "kobana-transfer": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote",
        "https://mcp.kobana.com.br/transfer/mcp",
        "--header",
        "Authorization: Bearer your_access_token"
      ]
    }
  }
}
```

### Remote MCP Sandbox

```json
{
  "mcpServers": {
    "kobana-transfer": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote",
        "https://mcp-sandbox.kobana.com.br/transfer/mcp",
        "--header",
        "Authorization: Bearer your_sandbox_token"
      ]
    }
  }
}
```

## OAuth Scopes

| Resource | Scope |
|----------|-------|
| Pix Transfers | `transfer.pix` |
| TED Transfers | `transfer.ted` |
| Internal Transfers | `transfer.internal` |
| Transfer Batches | `transfer.batches` |
| Financial Accounts | `financial.accounts` |

## Additional Resources

- **GitHub Repository**: https://github.com/universokobana/kobana-mcp-servers
- **npm Package**: https://www.npmjs.com/package/kobana-mcp-transfer
- **Kobana Documentation**: https://developers.kobana.com.br
- **API Specification**: https://github.com/universokobana/kobana-api-specs
