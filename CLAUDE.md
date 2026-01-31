# Claude Instructions for Kobana Skills

This repository contains Kobana's official skills for financial automation with Claude.

## Repository Structure

```
kobana-skills/
├── skills/
│   ├── api-charge-pix/       # Pix charges via REST API
│   ├── mcp-charge-pix/       # Pix charges via MCP Server
│   ├── api-transfer-pix/     # Pix transfers via REST API
│   ├── mcp-transfer-pix/     # Pix transfers via MCP Server
│   ├── api-payment-pix/      # Pix payments via REST API
│   └── mcp-payment-pix/      # Pix payments via MCP Server
├── spec/                     # Agent Skills specification
│   └── kobana-skills-structure.md  # Standard structure for new skills
└── template/                 # Skill template
```

## Creating New Skills

For developing new Kobana skills, follow the standard structure documented in:
- **Structure Guide**: `spec/kobana-skills-structure.md`

This guide includes:
- Directory structure for API-based and MCP-based skills
- Templates for SKILL.md and REFERENCE.md
- Naming conventions
- Checklist for new skill development

**IMPORTANT**: When creating or removing skills, you MUST update the `.claude-plugin/marketplace.json` file to register/unregister the skill in the `plugins[0].skills` array.

## Available Skills

### api-charge-pix
- **Purpose**: Create and manage Pix charges using Kobana REST API
- **Documentation**: `skills/api-charge-pix/SKILL.md`
- **Full Reference**: `skills/api-charge-pix/references/REFERENCE.md`

### mcp-charge-pix
- **Purpose**: Create and manage Pix charges using kobana-mcp-charge MCP server
- **Documentation**: `skills/mcp-charge-pix/SKILL.md`
- **Full Reference**: `skills/mcp-charge-pix/references/REFERENCE.md`

### api-transfer-pix
- **Purpose**: Create and manage Pix transfers using Kobana REST API
- **Documentation**: `skills/api-transfer-pix/SKILL.md`
- **Full Reference**: `skills/api-transfer-pix/references/REFERENCE.md`

### mcp-transfer-pix
- **Purpose**: Create and manage Pix transfers using kobana-mcp-transfer MCP server
- **Documentation**: `skills/mcp-transfer-pix/SKILL.md`
- **Full Reference**: `skills/mcp-transfer-pix/references/REFERENCE.md`

### api-payment-pix
- **Purpose**: Pay Pix charges and decode QR codes using Kobana REST API
- **Documentation**: `skills/api-payment-pix/SKILL.md`
- **Full Reference**: `skills/api-payment-pix/references/REFERENCE.md`

### mcp-payment-pix
- **Purpose**: Pay Pix charges and decode QR codes using kobana-mcp-payment MCP server
- **Documentation**: `skills/mcp-payment-pix/SKILL.md`
- **Full Reference**: `skills/mcp-payment-pix/references/REFERENCE.md`

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

### Use api-charge-pix when:
- Making direct HTTP calls to Kobana API
- Integrating with backend systems
- Need full control over API requests
- Working without MCP server configured

### Use mcp-charge-pix when:
- MCP server is already configured
- Using Claude Desktop or Claude Code with MCP
- Want simplified tool-based interaction
- Prefer not to handle HTTP requests directly

### Use api-transfer-pix when:
- Making direct HTTP calls to Kobana API
- Sending Pix transfers to suppliers, employees, or partners
- Need full control over transfer batches and approvals
- Working without MCP server configured

### Use mcp-transfer-pix when:
- MCP server is already configured
- Using Claude Desktop or Claude Code with MCP
- Want simplified tool-based interaction for transfers
- Prefer not to handle HTTP requests directly

### Use api-payment-pix when:
- Making direct HTTP calls to Kobana API
- Paying Pix charges (invoices, QR codes, copy-paste)
- Need to decode Pix EMV before paying
- Working without MCP server configured

### Use mcp-payment-pix when:
- MCP server is already configured
- Using Claude Desktop or Claude Code with MCP
- Want simplified tool-based interaction for Pix payments
- Prefer not to handle HTTP requests directly

## Authentication

Both skills require a Kobana access token:
- **API**: Pass via `Authorization: Bearer {token}` header
- **MCP**: Configure via `KOBANA_ACCESS_TOKEN` environment variable

## Quick Commands

### Create Pix Charge (API)
```bash
curl -X POST https://api.kobana.com.br/v2/charge/pix \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"amount": 100.00, "pix_account_uid": "...", "expire_at": "2026-02-01T12:00:00-03:00"}'
```

### Create Pix Charge (MCP)
```
Use tool: create_charge_pix
Parameters: {"amount": 100.00, "pix_account_uid": "...", "expire_at": "2026-02-01T12:00:00-03:00"}
```

### Create Pix Transfer (API)
```bash
curl -X POST https://api.kobana.com.br/v2/transfer/pix \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"amount": 100.00, "financial_account_uid": "...", "type": "key", "key_type": "email", "key": "recipient@example.com", "beneficiary": {"document_number": "111.222.333-44", "name": "João Silva"}}'
```

### Create Pix Transfer (MCP)
```
Use tool: create_transfer_pix
Parameters: {"amount": 100.00, "financial_account_uid": "...", "type": "key", "key_type": "email", "key": "recipient@example.com", "beneficiary": {"document_number": "111.222.333-44", "name": "João Silva"}}
```

### Pay Pix Charge (API)
```bash
curl -X POST https://api.kobana.com.br/v2/payment/pix \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"financial_account_uid": "...", "emv": "00020126580014br.gov.bcb.pix...", "amount": 150.00}'
```

### Pay Pix Charge (MCP)
```
Use tool: create_payment_pix
Parameters: {"financial_account_uid": "...", "emv": "00020126580014br.gov.bcb.pix...", "amount": 150.00}
```

## About Kobana

Kobana is a Brazilian financial automation platform connecting businesses to 40+ banks through a unified API. More info at https://www.kobana.com.br
