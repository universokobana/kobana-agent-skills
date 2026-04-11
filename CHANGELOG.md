# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.2.0] - 2026-04-11

### Added
- Root `LICENSE` (MIT) and standardized every skill to ship a `LICENSE` file (replacing `LICENSE.txt`).
- `Kobana Mailbox` remote MCP endpoint to the plugin and marketplace manifests.
- `license` field to `marketplace.json` metadata.

### Changed
- `marketplace.json` and `plugin.json` `mcpServers` now point to the hosted **remote** MCP endpoints at `https://mcp.kobana.com.br/{namespace}/mcp`, which use OAuth 2.1 + PKCE + Dynamic Client Registration. Users no longer need a static `KOBANA_ACCESS_TOKEN` to install the plugin.
- MCP server entries renamed to friendly display names: `Kobana Admin`, `Kobana Charge`, `Kobana Data`, `Kobana EDI`, `Kobana Financial`, `Kobana Mailbox`, `Kobana Payment`, `Kobana Transfer`.
- `plugin.json` `license` field updated to `SEE LICENSE IN LICENSE`.
- `SKILL.md` frontmatter and the structure spec now reference `LICENSE` instead of `LICENSE.txt`.

## [1.1.0] - 2026-01-31

### Changed
- Unified skills: removed the `api-*` / `mcp-*` split and merged each domain into a single skill (`charge-pix`, `transfer-pix`, `payment-pix`) that supports both MCP (preferred) and REST API (fallback) modes.
- Added stdio-based `mcpServers` configuration to the plugin and marketplace manifests so the plugin can pre-configure local MCP servers via `npx`.
- Updated READMEs and `CLAUDE.md` to document the unified structure and the new MCP setup flow for Claude Code and Claude Desktop.

## [1.0.0] - 2026-01-31

### Added
- Initial release of Kobana Agent Skills as a Claude Code plugin marketplace.
- Pix charge skills (`api-charge-pix`, `mcp-charge-pix`).
- Pix transfer skills (`api-transfer-pix`, `mcp-transfer-pix`).
- Pix payment skills (`api-payment-pix`, `mcp-payment-pix`).
- `spec/kobana-agent-skills-structure.md` documenting the standard structure for new Kobana skills.
- Skill template under `template/`.
- English and Portuguese READMEs.

[1.2.0]: https://github.com/universokobana/kobana-agent-skills/compare/v1.1.0...v1.2.0
[1.1.0]: https://github.com/universokobana/kobana-agent-skills/compare/v1.0.0...v1.1.0
[1.0.0]: https://github.com/universokobana/kobana-agent-skills/releases/tag/v1.0.0
