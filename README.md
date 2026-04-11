*[Leia em Português](README.pt-BR.md)*

> **Note:** This repository is a fork of [Anthropic's skills repository](https://github.com/anthropics/skills), adapted for [Kobana](https://ai.kobana.com.br) - a financial automation platform with native AI. For information about the Agent Skills standard, see [agentskills.io](http://agentskills.io).

# Skills

Skills are folders of instructions, scripts, and resources that Claude loads dynamically to improve performance on specialized tasks. Skills teach Claude how to complete specific tasks in a repeatable way, whether that's creating documents with your company's brand guidelines, analyzing data using your organization's specific workflows, or automating personal tasks.

For more information, check out:
- [What are skills?](https://support.claude.com/en/articles/12512176-what-are-skills)
- [Using skills in Claude](https://support.claude.com/en/articles/12512180-using-skills-in-claude)
- [How to create custom skills](https://support.claude.com/en/articles/12512198-creating-custom-skills)
- [Equipping agents for the real world with Agent Skills](https://anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)

# About This Repository

This repository contains Kobana's official skills for financial automation with Claude. These skills help you interact with Kobana's APIs and MCP servers for Pix charges, payments, and financial operations.

Each skill supports two modes: **MCP (preferred)** when the corresponding MCP server is configured, and **REST API (fallback)** when it's not.

## About Kobana

[Kobana](https://www.kobana.com.br) is a financial automation platform that connects businesses to over 40 Brazilian banks through a unified API. With more than R$ 50 billion in processed transactions and 5+ million monthly banking operations, Kobana offers payment, collection, and transfer solutions with native AI integration.

## Available Skills

| Skill | Description |
|-------|-------------|
| [charge-pix](./skills/charge-pix) | Create and manage Pix charges, accounts, and payments |
| [transfer-pix](./skills/transfer-pix) | Create and manage Pix transfers and batches |
| [payment-pix](./skills/payment-pix) | Pay Pix charges, decode QR codes, and manage payment batches |

## MCP Server Setup

The skills use Kobana's hosted **remote MCP servers**. Each endpoint at `https://mcp.kobana.com.br/{namespace}/mcp` implements OAuth 2.1 + PKCE + Dynamic Client Registration, so the client negotiates authentication in the browser — you never paste a client id, secret, or static token.

### Remote endpoints

| Namespace | URL |
|---|---|
| Admin     | `https://mcp.kobana.com.br/admin/mcp` |
| Charge    | `https://mcp.kobana.com.br/charge/mcp` |
| Data      | `https://mcp.kobana.com.br/data/mcp` |
| EDI       | `https://mcp.kobana.com.br/edi/mcp` |
| Financial | `https://mcp.kobana.com.br/financial/mcp` |
| Mailbox   | `https://mcp.kobana.com.br/mailbox/mcp` |
| Payment   | `https://mcp.kobana.com.br/payment/mcp` |
| Transfer  | `https://mcp.kobana.com.br/transfer/mcp` |

> For the sandbox, swap `mcp.kobana.com.br` for `mcp-sandbox.kobana.com.br`.

### Claude Code

When you install the plugin from this marketplace, the remote MCP servers are pre-configured automatically — you don't need to edit any file or set any environment variable. On first tool use, Claude Code opens your browser to complete the Kobana OAuth login.

If you prefer to register the servers manually, use the built-in `claude mcp add` command with `--transport http`:

```bash
claude mcp add --transport http kobana-financial https://mcp.kobana.com.br/financial/mcp
```

### Claude Desktop

Settings → **Connectors → Add Custom Connector**. Paste the URL of the namespace you want, leave the Advanced settings empty, click **Add**, and complete the Kobana login in the browser window that opens. Repeat for each namespace you need.

For setup instructions for other clients (Claude.ai web, ChatGPT, Cursor, VS Code, Windsurf, Manus, Perplexity), see the [kobana-mcp-servers README](https://github.com/universokobana/kobana-mcp-servers#using-with-mcp-clients).

## Disclaimer

**These skills are provided for demonstration and educational purposes only.** Always test skills thoroughly in your own environment before relying on them for critical tasks.

# Repository Structure

- [./skills](./skills): Kobana financial automation skills
- [./spec](./spec): The Agent Skills specification
  - [Kobana Agent Skills Structure](./spec/kobana-agent-skills-structure.md): Standard structure for skills
- [./template](./template): Skill template

# Try in Claude Code, Claude.ai, and the API

## Claude Code

You can register this repository as a Claude Code Plugin marketplace by running the following command in Claude Code:
```
/plugin marketplace add universokobana/kobana-agent-skills
```

Then, to install the Kobana skills:
1. Select `Browse and install plugins`
2. Select `kobana-agent-skills`
3. Select `kobana-agent-skills`
4. Select `Install now`

Alternatively, directly install via:
```
/plugin install kobana-agent-skills@kobana-agent-skills
```

After installing the plugin, you can use the skill by just mentioning it. For example:
- "Use the charge-pix skill to create a Pix charge of R$ 100.00"
- "Use the charge-pix skill to list my Pix accounts"
- "Use the transfer-pix skill to send a Pix transfer of R$ 500.00"
- "Use the transfer-pix skill to create a transfer batch"
- "Use the payment-pix skill to pay a Pix QR code"
- "Use the payment-pix skill to decode and pay a Pix invoice"

## Claude.ai

To use any skill from this repository or upload custom skills, follow the instructions in [Using skills in Claude](https://support.claude.com/en/articles/12512180-using-skills-in-claude#h_a4222fa77b).

## Claude API

You can use pre-built skills, and upload custom skills, via the Claude API. See the [Skills API Quickstart](https://docs.claude.com/en/api/skills-guide#creating-a-skill) for more.

# Creating a Basic Skill

Skills are simple to create - just a folder with a `SKILL.md` file containing YAML frontmatter and instructions. You can use the **template** in this repository as a starting point:

> **Important:** When creating or removing skills, you must update the following files:
> - `.claude-plugin/marketplace.json` - Register/unregister the skill in the `plugins[0].skills` array
> - `README.md` and `README.pt-BR.md` - Update the "Available Skills" table
> - `CLAUDE.md` - Update the repository structure and available skills sections

```markdown
---
name: my-skill-name
description: A clear description of what this skill does and when to use it
---

# My Skill Name

[Add your instructions here that Claude will follow when this skill is active]

## Examples
- Example usage 1
- Example usage 2

## Guidelines
- Guideline 1
- Guideline 2
```

The frontmatter requires only two fields:
- `name` - A unique identifier for your skill (lowercase, hyphens for spaces)
- `description` - A complete description of what the skill does and when to use it

The markdown content below contains the instructions, examples, and guidelines that Claude will follow. For more details, see [How to create custom skills](https://support.claude.com/en/articles/12512198-creating-custom-skills).

# Additional Resources

- [Kobana Website](https://www.kobana.com.br)
- [Kobana AI](https://ai.kobana.com.br)
- [Kobana API Documentation](https://developers.kobana.com.br)
- [Kobana MCP Servers](https://github.com/universokobana/kobana-mcp-servers)
- [Kobana API Specs](https://github.com/universokobana/kobana-api-specs)
