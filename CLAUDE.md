# Claude Instructions for Kobana Skills

This repository contains Kobana's official skills for financial automation with Claude.

## Repository Structure

```
kobana-agent-skills/
├── skills/
│   ├── charge-pix/            # Pix charges (MCP + API)
│   ├── transfer-pix/          # Pix transfers (MCP + API)
│   └── payment-pix/           # Pix payments (MCP + API)
├── spec/                      # Agent Skills specification
│   └── kobana-agent-skills-structure.md  # Standard structure for new skills
└── template/                  # Skill template
```

## Creating New Skills

For developing new Kobana skills, follow the standard structure documented in:
- **Structure Guide**: `spec/kobana-agent-skills-structure.md`

This guide includes:
- Directory structure for skills
- Templates for SKILL.md and REFERENCE.md
- Naming conventions
- Checklist for new skill development

**IMPORTANT**: When creating or removing skills, you MUST update the `.claude-plugin/marketplace.json` file to register/unregister the skill in the `plugins[0].skills` array.

## Available Skills

### charge-pix
- **Purpose**: Create and manage Pix charges, accounts, and payments
- **Modes**: MCP (kobana-mcp-charge) preferred, REST API fallback
- **Documentation**: `skills/charge-pix/SKILL.md`
- **Full Reference**: `skills/charge-pix/references/REFERENCE.md`

### transfer-pix
- **Purpose**: Create and manage Pix transfers and batches
- **Modes**: MCP (kobana-mcp-transfer) preferred, REST API fallback
- **Documentation**: `skills/transfer-pix/SKILL.md`
- **Full Reference**: `skills/transfer-pix/references/REFERENCE.md`

### payment-pix
- **Purpose**: Pay Pix charges, decode QR codes, and manage payment batches
- **Modes**: MCP (kobana-mcp-payment) preferred, REST API fallback
- **Documentation**: `skills/payment-pix/SKILL.md`
- **Full Reference**: `skills/payment-pix/references/REFERENCE.md`

## External Documentation

### Kobana API
- **API Documentation**: https://developers.kobana.com.br
- **API Specifications (OpenAPI)**: https://github.com/universokobana/kobana-api-specs
- **Base URLs**:
  - Production: `https://api.kobana.com.br`
  - Sandbox: `https://api-sandbox.kobana.com.br`

### Kobana MCP Servers
- **Repository**: https://github.com/universokobana/kobana-mcp-servers
- **npm packages**:
  - `kobana-mcp-charge` - Pix charges, accounts, payments
  - `kobana-mcp-admin` - Certificates, connections, users
  - `kobana-mcp-financial` - Financial accounts, balances
  - `kobana-mcp-payment` - Bank billets, Pix, taxes
  - `kobana-mcp-transfer` - Pix, TED, internal transfers
- **Remote MCP endpoints**:
  - Production: `https://mcp.kobana.com.br/{namespace}/mcp`
  - Sandbox: `https://mcp-sandbox.kobana.com.br/{namespace}/mcp`

## When to Use Each Skill

### Use charge-pix when:
- Creating Pix charges (instant or billing)
- Managing Pix accounts
- Listing, updating, or canceling charges
- Tracking charge payments and QR codes

### Use transfer-pix when:
- Sending Pix transfers to suppliers, employees, or partners
- Managing transfer batches and approvals
- Sending money to Pix keys or bank accounts

### Use payment-pix when:
- Paying Pix charges (invoices, QR codes, copy-paste)
- Decoding Pix EMV before paying
- Managing payment batches and approvals

## Operation Modes

Each skill supports two modes:

1. **MCP (preferred)** — If the corresponding MCP server is configured, use MCP tools directly
2. **REST API (fallback)** — If MCP is not available, use HTTP calls to the Kobana API

> Skills should always try MCP tools first and only fall back to the REST API if MCP is not available.

## Authentication

Both modes require a Kobana access token:
- **MCP**: Configure via `KOBANA_ACCESS_TOKEN` environment variable
- **API**: Pass via `Authorization: Bearer {token}` header

## Quick Commands

### Create Pix Charge (MCP)
```
Use tool: create_charge_pix
Parameters: {"amount": 100.00, "pix_account_uid": "...", "expire_at": "2026-02-01T12:00:00-03:00"}
```

### Create Pix Charge (API)
```bash
curl -X POST https://api.kobana.com.br/v2/charge/pix \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"amount": 100.00, "pix_account_uid": "...", "expire_at": "2026-02-01T12:00:00-03:00"}'
```

### Create Pix Transfer (MCP)
```
Use tool: create_transfer_pix
Parameters: {"amount": 100.00, "financial_account_uid": "...", "type": "key", "key_type": "email", "key": "recipient@example.com", "beneficiary": {"document_number": "111.222.333-44", "name": "João Silva"}}
```

### Create Pix Transfer (API)
```bash
curl -X POST https://api.kobana.com.br/v2/transfer/pix \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"amount": 100.00, "financial_account_uid": "...", "type": "key", "key_type": "email", "key": "recipient@example.com", "beneficiary": {"document_number": "111.222.333-44", "name": "João Silva"}}'
```

### Pay Pix Charge (MCP)
```
Use tool: create_payment_pix
Parameters: {"financial_account_uid": "...", "emv": "00020126580014br.gov.bcb.pix...", "amount": 150.00}
```

### Pay Pix Charge (API)
```bash
curl -X POST https://api.kobana.com.br/v2/payment/pix \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"financial_account_uid": "...", "emv": "00020126580014br.gov.bcb.pix...", "amount": 150.00}'
```

## About Kobana

Kobana is a Brazilian financial automation platform connecting businesses to 40+ banks through a unified API. More info at https://www.kobana.com.br
