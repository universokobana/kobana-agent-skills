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

## About Kobana

[Kobana](https://www.kobana.com.br) is a financial automation platform that connects businesses to over 40 Brazilian banks through a unified API. With more than R$ 50 billion in processed transactions and 5+ million monthly banking operations, Kobana offers payment, collection, and transfer solutions with native AI integration.

## Available Skills

| Skill | Description |
|-------|-------------|
| [api-charge-pix](./skills/api-charge-pix) | Create and manage Pix charges using Kobana REST API |
| [mcp-charge-pix](./skills/mcp-charge-pix) | Create and manage Pix charges using kobana-mcp-charge MCP server |
| [api-transfer-pix](./skills/api-transfer-pix) | Create and manage Pix transfers using Kobana REST API |
| [mcp-transfer-pix](./skills/mcp-transfer-pix) | Create and manage Pix transfers using kobana-mcp-transfer MCP server |
| [api-payment-pix](./skills/api-payment-pix) | Pay Pix charges and decode QR codes using Kobana REST API |
| [mcp-payment-pix](./skills/mcp-payment-pix) | Pay Pix charges and decode QR codes using kobana-mcp-payment MCP server |

## Disclaimer

**These skills are provided for demonstration and educational purposes only.** Always test skills thoroughly in your own environment before relying on them for critical tasks.

# Repository Structure

- [./skills](./skills): Kobana financial automation skills
- [./spec](./spec): The Agent Skills specification
  - [Kobana Agent Skills Structure](./spec/kobana-agent-skills-structure.md): Standard structure for API and MCP-based skills
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
- "Use the api-charge-pix skill to create a Pix charge of R$ 100.00"
- "Use the mcp-charge-pix skill to list my Pix accounts"
- "Use the api-transfer-pix skill to send a Pix transfer of R$ 500.00"
- "Use the mcp-transfer-pix skill to create a transfer batch"
- "Use the api-payment-pix skill to pay a Pix QR code"
- "Use the mcp-payment-pix skill to decode and pay a Pix invoice"

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
