# MCP Integration Guide

How to add Model Context Protocol (MCP) servers to Claude Code plugins.

## Table of Contents

1. [What is MCP?](#what-is-mcp)
2. [MCP in Claude Code Plugins](#mcp-in-claude-code-plugins)
3. [Configuration Format](#configuration-format)
4. [Common MCP Server Types](#common-mcp-server-types)
5. [Testing MCP Integrations](#testing-mcp-integrations)
6. [Example Configurations](#example-configurations)
7. [Best Practices](#best-practices)

## What is MCP?

**Model Context Protocol (MCP)** is a standardized protocol that allows Claude to interact with external tools and data sources through a consistent interface.

### Key Concepts

**MCP Server:** An external service that exposes tools, resources, or prompts to Claude
**MCP Client:** Claude Code acts as the client, connecting to MCP servers
**Tools:** Functions the MCP server provides (e.g., database queries, API calls)
**Resources:** Data the MCP server can provide (e.g., files, documents)
**Prompts:** Pre-defined prompt templates the server offers

### Why Use MCP?

✅ **Benefits:**
- Access external APIs and services
- Query databases directly
- Integrate with third-party tools
- Share MCP servers across plugins
- Standardized protocol for tool integration

❌ **When NOT to use MCP:**
- Simple bash commands (use hooks instead)
- Static content (use skills instead)
- Built-in Claude Code functionality exists

## MCP in Claude Code Plugins

Plugins can include MCP server configurations in `.mcp.json` at the plugin root.

### File Location

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json
├── .mcp.json              # MCP configuration
├── commands/
├── skills/
└── README.md
```

### Plugin Path Variable

Use `${CLAUDE_PLUGIN_ROOT}` to reference files within your plugin:

```json
{
  "mcpServers": {
    "my-server": {
      "command": "node",
      "args": ["${CLAUDE_PLUGIN_ROOT}/server/index.js"]
    }
  }
}
```

This ensures paths work regardless of where the plugin is installed.

## Configuration Format

### Basic Structure

```json
{
  "mcpServers": {
    "server-name": {
      "command": "command-to-run",
      "args": ["arg1", "arg2"],
      "env": {
        "ENV_VAR": "value"
      }
    }
  }
}
```

### Field Reference

| Field | Required | Type | Description |
|-------|----------|------|-------------|
| `command` | ✅ Yes | string | Executable command (node, python, etc.) |
| `args` | ✅ Yes | array | Command arguments |
| `env` | ❌ No | object | Environment variables for the server |
| `disabled` | ❌ No | boolean | Set to true to disable the server |

### Environment Variables

**From user's environment:**
```json
{
  "env": {
    "API_KEY": "${NOTION_API_KEY}"
  }
}
```

**Hard-coded (avoid for secrets):**
```json
{
  "env": {
    "API_ENDPOINT": "https://api.example.com"
  }
}
```

**Best practice:** Document required environment variables in README

## Common MCP Server Types

### 1. Node.js Servers

**Standard MCP Server Package:**
```json
{
  "mcpServers": {
    "notion": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-notion"],
      "env": {
        "NOTION_API_KEY": "${NOTION_API_KEY}"
      }
    }
  }
}
```

**Local Server Script:**
```json
{
  "mcpServers": {
    "custom-server": {
      "command": "node",
      "args": ["${CLAUDE_PLUGIN_ROOT}/server/index.js"],
      "env": {
        "PORT": "3000"
      }
    }
  }
}
```

### 2. Python Servers

**Python Package:**
```json
{
  "mcpServers": {
    "python-server": {
      "command": "python",
      "args": ["-m", "mcp_server_package"],
      "env": {
        "API_KEY": "${API_KEY}"
      }
    }
  }
}
```

**Local Python Script:**
```json
{
  "mcpServers": {
    "data-server": {
      "command": "python",
      "args": ["${CLAUDE_PLUGIN_ROOT}/server/main.py"]
    }
  }
}
```

### 3. HTTP/SSE Servers

**Server-Sent Events:**
```json
{
  "mcpServers": {
    "remote-server": {
      "command": "mcp-client",
      "args": ["--sse", "https://api.example.com/mcp"]
    }
  }
}
```

### 4. Docker-Based Servers

```json
{
  "mcpServers": {
    "docker-server": {
      "command": "docker",
      "args": [
        "run", "--rm", "-i",
        "mcp-server-image:latest"
      ]
    }
  }
}
```

## Testing MCP Integrations

### Local Testing Workflow

**1. Install plugin locally:**
```bash
cd /path/to/plugin
/plugin install .
```

**2. Verify MCP server starts:**
```bash
# Check Claude Code logs for MCP server startup
# Should see: "Connected to MCP server: server-name"
```

**3. Test MCP tools:**
```bash
# In Claude Code session, try using tools from the MCP server
# Example: "Use the [tool-name] tool to [action]"
```

**4. Check for errors:**
```bash
# Look for connection errors, authentication failures, etc.
tail -f ~/.claude/logs/mcp-server-name.log
```

### Common Issues

**Server won't start:**
- Check command is in PATH
- Verify args are correct
- Ensure ${CLAUDE_PLUGIN_ROOT} resolves properly
- Check required dependencies installed

**Authentication failures:**
- Verify environment variables are set
- Check API keys are valid
- Ensure .env file is loaded

**Tools not appearing:**
- Confirm server implements MCP protocol correctly
- Check server logs for errors
- Verify Claude Code version supports MCP

## Example Configurations

### Example 1: Notion MCP Server

```json
{
  "mcpServers": {
    "notion": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-notion"],
      "env": {
        "NOTION_API_KEY": "${NOTION_API_KEY}"
      }
    }
  }
}
```

**Required setup:**
```bash
# User needs to set environment variable
export NOTION_API_KEY="secret_..."
```

**Provides:**
- Search Notion workspace
- Create/update pages
- Query databases

### Example 2: Database MCP Server

```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "POSTGRES_URL": "${DATABASE_URL}"
      }
    }
  }
}
```

**Required setup:**
```bash
export DATABASE_URL="postgresql://user:pass@localhost/db"
```

**Provides:**
- Execute SQL queries
- Read table schemas
- Manage database connections

### Example 3: Custom Local Server

```json
{
  "mcpServers": {
    "crm-server": {
      "command": "node",
      "args": ["${CLAUDE_PLUGIN_ROOT}/server/crm-server.js"],
      "env": {
        "CRM_API_KEY": "${CRM_API_KEY}",
        "CRM_URL": "https://crm.example.com"
      }
    }
  }
}
```

**Server implementation:**
```javascript
// ${CLAUDE_PLUGIN_ROOT}/server/crm-server.js
const { MCPServer } = require('@modelcontextprotocol/sdk');

const server = new MCPServer({
  name: 'crm-server',
  version: '1.0.0'
});

server.addTool({
  name: 'search_contacts',
  description: 'Search CRM contacts',
  inputSchema: {
    type: 'object',
    properties: {
      query: { type: 'string' }
    }
  },
  async handler({ query }) {
    // Implementation
  }
});

server.start();
```

### Example 4: Multiple MCP Servers

```json
{
  "mcpServers": {
    "notion": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-notion"],
      "env": {
        "NOTION_API_KEY": "${NOTION_API_KEY}"
      }
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    },
    "custom-tools": {
      "command": "node",
      "args": ["${CLAUDE_PLUGIN_ROOT}/server/tools.js"]
    }
  }
}
```

## Best Practices

### Configuration

✅ **Do:**
- Use `${CLAUDE_PLUGIN_ROOT}` for plugin-relative paths
- Document all required environment variables in README
- Provide example .env file
- Use standard MCP packages when available
- Keep server configurations simple

❌ **Don't:**
- Hard-code secrets or API keys
- Use absolute paths
- Run servers that require root/admin
- Include unused MCP servers
- Skip documenting setup requirements

### Security

✅ **Do:**
- Validate environment variables are set
- Use HTTPS for remote servers
- Document security requirements
- Warn about sensitive permissions
- Use principle of least privilege

❌ **Don't:**
- Store credentials in .mcp.json
- Expose internal services unnecessarily
- Skip authentication for sensitive operations
- Ignore error messages with secrets

### Documentation

**README should include:**
```markdown
## Setup

### Environment Variables

Create `.env` file with:

\`\`\`bash
NOTION_API_KEY=secret_...     # Get from https://notion.so/my-integrations
CRM_API_KEY=key_...           # Get from CRM settings
\`\`\`

### MCP Server

This plugin includes a Notion MCP server that provides:
- Search workspace
- Create/update pages
- Query databases

The server starts automatically when the plugin loads.
```

### Performance

✅ **Do:**
- Keep servers lightweight
- Implement timeout handling
- Cache responses when appropriate
- Monitor resource usage
- Provide health checks

❌ **Don't:**
- Run expensive operations on startup
- Keep unnecessary connections open
- Skip error handling
- Ignore memory leaks

### Maintenance

**Regular checks:**
- [ ] MCP SDK dependencies up to date
- [ ] Server still compatible with Claude Code
- [ ] External APIs haven't changed
- [ ] Error handling still robust
- [ ] Documentation still accurate

**Version updates:**
```json
{
  "mcpServers": {
    "notion": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-notion@1.0.0"  // Pin version
      ],
      "env": {
        "NOTION_API_KEY": "${NOTION_API_KEY}"
      }
    }
  }
}
```

## Troubleshooting

### Server Won't Start

**Check command availability:**
```bash
which node  # or python, docker, etc.
node --version
```

**Test server manually:**
```bash
cd ~/.claude/plugins/plugin-name
node server/index.js  # Run server directly
```

**Review logs:**
```bash
tail -f ~/.claude/logs/mcp-*.log
```

### Authentication Errors

**Verify environment variables:**
```bash
echo $NOTION_API_KEY  # Should output key
```

**Check .env is loaded:**
```bash
# Add to plugin README
source .env  # Before starting Claude Code
```

**Test API credentials:**
```bash
curl -H "Authorization: Bearer $API_KEY" https://api.example.com/test
```

### Tools Not Available

**Verify server implements MCP protocol:**
- Server must respond to `initialize` request
- Must list available tools
- Must implement tool execution

**Check Claude Code version:**
```bash
claude --version  # Ensure MCP support included
```

**Test with simple MCP server:**
```bash
npx -y @modelcontextprotocol/server-hello-world
```

## Resources

- [MCP Specification](https://spec.modelcontextprotocol.io/)
- [MCP SDK Documentation](https://github.com/modelcontextprotocol/sdk)
- [Official MCP Servers](https://github.com/modelcontextprotocol/servers)
- [Plugin Development Guide](./plugin-development.md)
- [Marketplace Management Guide](./marketplace-management.md)

## Next Steps

1. Review official MCP servers for examples
2. Identify external tools to integrate
3. Create or install MCP server
4. Configure in .mcp.json
5. Document setup requirements
6. Test thoroughly
7. Add to plugin
