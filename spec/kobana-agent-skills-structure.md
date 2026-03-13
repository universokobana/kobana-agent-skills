# Estrutura Padrão de Skills Kobana

Este documento define a estrutura padrão para desenvolvimento de skills Kobana. Cada skill suporta dois modos de operação: **MCP (preferido)** e **API REST (fallback)**.

## Estrutura de Diretórios

```
skills/
└── {namespace}/               # Skill unificada (MCP + API)
    ├── SKILL.md               # Instruções principais (obrigatório)
    ├── LICENSE.txt            # Licença (obrigatório)
    └── references/
        └── REFERENCE.md       # Referência técnica completa
```

## Convenção de Nomenclatura

| Padrão | Exemplo |
|--------|---------|
| `{domínio}-{recurso}` | `charge-pix`, `payment-billet`, `transfer-ted` |

O `{namespace}` deve seguir o padrão:
- Lowercase apenas
- Separado por hífens
- Formato: `{domínio}-{recurso}` (ex: `charge-pix`, `payment-billet`, `transfer-ted`)

---

## Estrutura do SKILL.md

### Frontmatter

```yaml
---
name: {namespace}
description: Guide for [descrição]. Use when the user wants to [casos de uso]. Supports both MCP tools (preferred) and REST API.
license: Complete terms in LICENSE.txt
metadata:
  author: Kobana
  version: "X.Y"
---
```

### Template Completo

```markdown
---
name: {namespace}
description: Guide for managing [recursos] using Kobana. Use when the user wants to [lista de ações]. Supports both MCP tools (preferred) and REST API.
license: Complete terms in LICENSE.txt
metadata:
  author: Kobana
  version: "1.0"
---

# Kobana {Recurso} Skill

[Descrição curta do propósito da skill]

## How It Works

This skill supports two modes of operation:

1. **MCP (preferred)** — If the `kobana-mcp-{package}` MCP server is available, use MCP tools directly. This is simpler and recommended.
2. **REST API (fallback)** — If MCP is not available, use HTTP calls to the Kobana API.

> **Rule:** Always try MCP tools first. Only fall back to the REST API if MCP tools are not available or not configured.

---

## MCP Mode

### Available MCP Tools

#### {Recurso}
| Tool | Description |
|------|-------------|
| `list_{resource}` | List all |
| `create_{resource}` | Create new |
| `get_{resource}` | Get specific |

### {Ação Principal} (MCP)

#### Step 1: [Pré-requisito]

```
Use tool: list_{prerequisite}
```

#### Step 2: [Ação Principal]

Use the `create_{resource}` tool with the required parameters:

```json
{
  "param1": "value1",
  "param2": "value2"
}
```

### MCP Server Setup

Add to `.mcp.json` in your project root (Claude Code) or `~/Library/Application Support/Claude/claude_desktop_config.json` (Claude Desktop macOS):

```json
{
  "mcpServers": {
    "kobana-{package}": {
      "command": "npx",
      "args": ["-y", "kobana-mcp-{package}"],
      "env": {
        "KOBANA_ACCESS_TOKEN": "your_access_token"
      }
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

#### {Recurso Principal}
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v2/{path}` | List all |
| POST | `/v2/{path}` | Create new |
| GET | `/v2/{path}/{uid}` | Get specific |

### {Ação Principal} (API)

```json
POST /v2/{path}

{
  "param1": "value1",
  "param2": "value2"
}
```

---

## Common Parameters

### Required
| Parameter | Type | Description |
|-----------|------|-------------|

### Optional
| Parameter | Type | Description |
|-----------|------|-------------|

## Status Reference

| Status | Description |
|--------|-------------|

## Best Practices

1. **Prefer MCP when available** - Simpler and more reliable
2. **Use external_id** - Track in your system
3. **Test in sandbox** - Before production

## Reference Documentation

See [references/REFERENCE.md](references/REFERENCE.md) for complete documentation.
```

---

## Estrutura do REFERENCE.md

```markdown
# Kobana {Domínio} - Complete Reference

Complete documentation for [recursos]. Covers both MCP tools and REST API.

---

## MCP Server Reference

**npm package**: [kobana-mcp-{package}](https://www.npmjs.com/package/kobana-mcp-{package})

### Quick Start

```bash
KOBANA_ACCESS_TOKEN=your_token npx kobana-mcp-{package}
```

### Configuration

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `KOBANA_ACCESS_TOKEN` | Yes | - | Bearer access token |
| `KOBANA_API_URL` | No | `https://api.kobana.com.br` | Base URL |

### All Available MCP Tools (N tools)

#### {Recurso} (N tools)
| Tool | Description |
|------|-------------|

### MCP Tool Parameters

#### create_{resource}

**Required Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|

**Optional Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|

### Client Configurations

#### Claude Desktop (macOS)
#### Claude Desktop (Windows)
#### Claude Code
#### Remote MCP (Hosted)
#### Remote MCP Sandbox

### OAuth Scopes

| Resource | Scope |
|----------|-------|

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
| `X-Idempotency-Key` | No | Prevents replay processing |

### {Recurso}

#### List
```
GET /v2/{path}
```

#### Create
```
POST /v2/{path}
```

#### Get / Update / Delete
```
GET /v2/{path}/{uid}
PUT /v2/{path}/{uid}
DELETE /v2/{path}/{uid}
```

---

## Response Structure

```json
{
  "status": 201,
  "data": { ... }
}
```

## Status Reference

| Status | Description |
|--------|-------------|

## Error Responses

### 401 Unauthorized
### 422 Validation Error

## Webhooks

| Event | Description |
|-------|-------------|

---

## Additional Resources

- **GitHub Repository**: https://github.com/universokobana/kobana-mcp-servers
- **npm Package**: https://www.npmjs.com/package/kobana-mcp-{package}
- **Kobana Documentation**: https://developers.kobana.com.br
- **API Specification**: https://github.com/universokobana/kobana-api-specs
```

---

## Checklist para Nova Skill

### Antes de Começar

- [ ] Definir o namespace: `{domínio}-{recurso}`
- [ ] Identificar os endpoints/tools principais
- [ ] Identificar o pacote MCP correspondente

### Arquivos Obrigatórios

- [ ] `SKILL.md` com frontmatter válido
- [ ] `LICENSE.txt` com termos de licença
- [ ] `references/REFERENCE.md` com documentação completa

### Conteúdo do SKILL.md

- [ ] Frontmatter com `name`, `description`, `license`, `metadata`
- [ ] Seção "How It Works" explicando MCP preferido + API fallback
- [ ] Seção MCP Mode com tools disponíveis e exemplos
- [ ] Seção MCP Server Setup
- [ ] Seção API Mode com endpoints e exemplos
- [ ] Seção de Common Parameters
- [ ] Seção de Status Reference
- [ ] Seção de Best Practices
- [ ] Link para REFERENCE.md

### Conteúdo do REFERENCE.md

- [ ] Seção MCP Server Reference com todos os tools e parâmetros
- [ ] Seção REST API Reference com todos os endpoints e parâmetros
- [ ] Configurações de cliente MCP (Desktop, Code, Remote)
- [ ] OAuth Scopes
- [ ] Estrutura de resposta com exemplos JSON
- [ ] Tabelas de status
- [ ] Códigos de erro
- [ ] Webhooks disponíveis
- [ ] Links para recursos externos

### Validação Final

- [ ] Nome do diretório = campo `name` do frontmatter
- [ ] `description` clara sobre O QUE faz e QUANDO usar
- [ ] Exemplos testados no ambiente sandbox
- [ ] Links funcionando
- [ ] Registrado no `.claude-plugin/marketplace.json`

---

## Mapeamento Kobana Skills

| Domínio | Skill | MCP Package |
|---------|-------|-------------|
| Charge - Pix | `charge-pix` | `kobana-mcp-charge` |
| Charge - Billet | `charge-billet` | `kobana-mcp-charge` |
| Payment - Billet | `payment-billet` | `kobana-mcp-payment` |
| Payment - Pix | `payment-pix` | `kobana-mcp-payment` |
| Payment - Tax | `payment-tax` | `kobana-mcp-payment` |
| Transfer - Pix | `transfer-pix` | `kobana-mcp-transfer` |
| Transfer - TED | `transfer-ted` | `kobana-mcp-transfer` |
| Admin | `admin` | `kobana-mcp-admin` |
| Financial | `financial` | `kobana-mcp-financial` |

---

## Recursos Externos

- **Agent Skills Spec**: [spec/agent-skills-spec.md](agent-skills-spec.md)
- **Kobana API Docs**: https://developers.kobana.com.br
- **Kobana API Specs**: https://github.com/universokobana/kobana-api-specs
- **Kobana MCP Servers**: https://github.com/universokobana/kobana-mcp-servers
