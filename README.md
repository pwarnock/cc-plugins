# Claude Plugins

Personal Claude Code plugin marketplace.

## Plugins

| Plugin | Description |
|--------|-------------|
| [personal-crm](https://github.com/pwarnock/personal-crm) | Capture meetups, contacts, and interactions from natural language into Notion CRM |
| [Notion](https://github.com/makenotion/claude-code-notion-plugin) | Notion Skills + Notion MCP server packaged as a Claude Code plugin |
| [kjbc](https://github.com/pwarnock/kjbc-mcp) | Strong's Concordance MCP - Grounded Greek/Hebrew word lookups for AI |
| [vercel-skills](https://github.com/pwarnock/vercel-skills) | Curated Vercel developer toolkit: React best practices, web design guidelines, browser automation, and deployment |

## Installation

1. Add marketplace to Claude Code:
   ```
   /plugin marketplace add pwarnock/pwarnock-cc-plugins
   ```

2. Install a plugin:
   ```
   /plugin install personal-crm@pwarnock-cc-plugins
   ```

## Plugin Configuration

Each plugin may require environment variables. See individual plugin READMEs for setup instructions.

## Documentation

Comprehensive guides for plugin development and marketplace management:

- **[Plugin Development](docs/plugin-development.md)** - Plugin structure, manifest specification, and best practices
- **[Skill Curation](docs/skill-curation.md)** - Evaluating and wrapping external skills
- **[MCP Integration](docs/mcp-integration.md)** - Adding Model Context Protocol servers to plugins
- **[Marketplace Management](docs/marketplace-management.md)** - Managing and versioning marketplace entries

See also the `create-plugin` skill in `skills/create-plugin/` for guided plugin creation.
