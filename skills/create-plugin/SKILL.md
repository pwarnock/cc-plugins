---
name: create-plugin
description: Use when creating a new Claude Code plugin. Guides through plugin structure, manifest creation, and best practices. Trigger phrases "create a plugin", "new plugin", "build a Claude Code plugin", "scaffold a plugin"
---

# Create Claude Code Plugin

Guides you through creating a properly structured Claude Code plugin from scratch.

## When to Use

Use this skill when:
- Starting a new plugin from scratch
- Converting existing code/skills to plugin format
- Adding a plugin to the marketplace
- Scaffolding a plugin structure
- Learning Claude Code plugin development

Don't use this skill for:
- Modifying existing plugins (just edit directly)
- Installing plugins (use `/plugin install`)
- Debugging plugin issues (use general debugging)

## Process

### Step 1: Define Plugin Scope

First, clarify the plugin's purpose:

**Questions to answer:**
1. What problem does this plugin solve?
2. Who is the target user?
3. What features are essential vs. nice-to-have?
4. Does it require external services/APIs?
5. Will it need MCP servers?

**Example:**
- Problem: Need to track contacts in Notion from conversations
- Users: People with Notion workspaces
- Essential: Capture contact info, add to database
- External: Notion API
- MCP: Yes, Notion MCP server

### Step 2: Choose Plugin Components

Decide which components your plugin needs:

**Commands** (`commands/*.md`)
- User-invocable slash commands
- Example: `/sync-contacts`, `/search-crm`
- Use when: Users need explicit control

**Agents** (`agents/*.md`)
- Specialized sub-agents for tasks
- Example: `contact-extractor`, `notion-writer`
- Use when: Complex multi-step tasks

**Skills** (`skills/*/SKILL.md`)
- Workflows, patterns, best practices
- Example: `contact-capture-workflow`
- Use when: Reusable processes or guidelines

**Hooks** (`hooks/*.sh`)
- Event-triggered shell scripts
- Example: `pre-commit.sh`, `session-start.sh`
- Use when: Automatic actions on events

**MCP Servers** (`.mcp.json`)
- External tool integrations
- Example: Notion, GitHub, database
- Use when: Need external APIs/services

**Typical combinations:**
- Simple plugin: Just commands
- Medium plugin: Commands + skills
- Complex plugin: Commands + agents + skills + MCP
- Integration plugin: MCP + commands + documentation

### Step 3: Create Repository Structure

Create the plugin directory and required files:

```bash
# Create plugin structure
mkdir plugin-name
cd plugin-name
mkdir .claude-plugin commands agents skills hooks

# Initialize git
git init
```

**Minimal required structure:**
```
plugin-name/
├── .claude-plugin/
│   └── plugin.json          # Required
├── README.md                # Highly recommended
└── LICENSE                  # Recommended
```

**Full structure:**
```
plugin-name/
├── .claude-plugin/
│   └── plugin.json
├── .mcp.json                # If using MCP servers
├── commands/
│   └── command-name.md
├── agents/
│   └── agent-name.md
├── skills/
│   └── skill-name/
│       └── SKILL.md
├── hooks/
│   └── hook-name.sh
├── server/                  # If custom MCP server
│   └── index.js
├── .gitignore
├── README.md
├── LICENSE
└── CHANGELOG.md
```

### Step 4: Write Plugin Manifest

Create `.claude-plugin/plugin.json`:

**Minimal manifest:**
```json
{
  "name": "plugin-name",
  "version": "1.0.0",
  "description": "Brief description of what the plugin does"
}
```

**Complete manifest:**
```json
{
  "name": "plugin-name",
  "version": "1.0.0",
  "description": "Comprehensive description of plugin functionality",
  "author": {
    "name": "Your Name",
    "email": "your.email@example.com",
    "url": "https://github.com/yourusername"
  },
  "homepage": "https://github.com/yourusername/plugin-name",
  "repository": "https://github.com/yourusername/plugin-name",
  "license": "MIT",
  "keywords": ["keyword1", "keyword2"],
  "dependencies": {
    "node": ">=18.0.0"
  }
}
```

**Guidelines:**
- Name: Use kebab-case (e.g., `personal-crm`, not `personalCRM`)
- Version: Follow semantic versioning (MAJOR.MINOR.PATCH)
- Description: Clear, concise, searchable
- Keywords: Help users discover your plugin

### Step 5: Add Components

Add components based on your Step 2 decisions:

#### Commands

Create `commands/command-name.md`:

```markdown
---
name: command-name
description: Brief description shown in command list
---

# Command Title

Detailed documentation and implementation.

## Usage

\`\`\`bash
/command-name [args]
\`\`\`

## Arguments

- `arg1` - Description of first argument
- `arg2` - Optional description of second argument

## Examples

\`\`\`bash
/command-name value1 value2
\`\`\`

## Implementation

[Detailed steps Claude should follow]
```

#### Agents

Create `agents/agent-name.md`:

```markdown
---
name: agent-name
description: When to use this agent
tools: [Read, Write, Bash]  # Optional: limit available tools
color: blue                  # Optional: UI color
---

# Agent System Prompt

You are a specialized agent for [specific task].

## Capabilities

- Capability 1
- Capability 2
- Capability 3

## Process

When invoked, follow these steps:
1. Step one
2. Step two
3. Step three

## Examples

[Provide usage examples]
```

#### Skills

Create `skills/skill-name/SKILL.md`:

```markdown
---
name: skill-name
description: When and how to use this skill. Include trigger phrases.
---

# Skill Title

Detailed workflow or pattern description.

## When to Use

Use this skill when:
- Scenario 1
- Scenario 2
- Scenario 3

## Process

1. **Step one:** Description
2. **Step two:** Description
3. **Step three:** Description

## Examples

[Usage examples]

## Best Practices

- Practice 1
- Practice 2
```

#### Hooks

Create `hooks/hook-name.sh`:

```bash
#!/bin/bash
# Description of what this hook does

# Hook receives event data via environment
echo "Hook triggered: $HOOK_EVENT"

# Perform actions
# ...

# Exit 0 for success, non-zero to block
exit 0
```

Make executable:
```bash
chmod +x hooks/hook-name.sh
```

#### MCP Configuration

Create `.mcp.json` if using MCP servers:

```json
{
  "mcpServers": {
    "server-name": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-package"],
      "env": {
        "API_KEY": "${API_KEY}"
      }
    }
  }
}
```

### Step 6: Write Documentation

Create comprehensive `README.md`:

```markdown
# Plugin Name

Brief description of what the plugin does.

## Features

- Feature 1
- Feature 2
- Feature 3

## Installation

\`\`\`bash
# Add marketplace (if applicable)
/plugin marketplace add owner/marketplace-repo

# Install plugin
/plugin install plugin-name@marketplace-name
\`\`\`

Or install directly:
\`\`\`bash
/plugin install owner/plugin-name
\`\`\`

## Configuration

### Environment Variables

Create a \`.env\` file with:

\`\`\`bash
API_KEY=your_api_key_here
OTHER_VAR=value
\`\`\`

### Setup Steps

1. Step one
2. Step two
3. Step three

## Usage

### Commands

- \`/command-name\` - Description

### Skills

The plugin includes these skills:
- **skill-name** - Triggers when [condition]

## Examples

### Example 1: Basic Usage

\`\`\`
User: Do something with the plugin
Claude: [Uses plugin to accomplish task]
\`\`\`

### Example 2: Advanced Usage

\`\`\`
User: /command-name with arguments
Claude: [Executes command]
\`\`\`

## Development

### Local Testing

\`\`\`bash
cd /path/to/plugin
/plugin install .
\`\`\`

### Running Tests

\`\`\`bash
npm test
\`\`\`

## Troubleshooting

### Issue 1

**Problem:** Description of problem

**Solution:** How to fix it

### Issue 2

**Problem:** Description of problem

**Solution:** How to fix it

## License

MIT License - see LICENSE file for details

## Credits

Credit any sources, inspirations, or contributors
```

### Step 7: Add Supporting Files

#### .gitignore

```
# Dependencies
node_modules/
*.log

# Environment
.env
.env.local

# OS files
.DS_Store
Thumbs.db

# IDE
.vscode/
.idea/
```

#### LICENSE

Choose an appropriate license (MIT is common):

```
MIT License

Copyright (c) 2026 [Your Name]

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

#### CHANGELOG.md

```markdown
# Changelog

All notable changes to this project will be documented in this file.

## [Unreleased]

## [1.0.0] - 2026-01-19

### Added
- Initial release
- Feature 1
- Feature 2
- Feature 3
```

### Step 8: Test Locally

Test your plugin before publishing:

```bash
# Install from local directory
cd /path/to/plugin
/plugin install .

# Verify installation
ls ~/.claude/plugins/plugin-name/

# Test components
# - Try commands: /command-name
# - Trigger skills by describing tasks
# - Check hooks execute on events
# - Test MCP server connections

# Check for errors
tail -f ~/.claude/logs/*.log
```

**Test checklist:**
- [ ] Plugin installs without errors
- [ ] Manifest is valid JSON
- [ ] Commands appear and work
- [ ] Skills trigger appropriately
- [ ] Hooks execute correctly
- [ ] MCP servers connect
- [ ] Environment variables load
- [ ] Documentation is accurate
- [ ] No security issues

### Step 9: Publish

**Create GitHub repository:**
```bash
# Create repo on GitHub first, then:
git remote add origin git@github.com:username/plugin-name.git
git add .
git commit -m "Initial plugin implementation"
git tag v1.0.0
git push -u origin main
git push --tags
```

**Create release:**
1. Go to GitHub repository
2. Click "Releases" → "Create a new release"
3. Tag: `v1.0.0`
4. Title: "Initial Release"
5. Description: List features and usage
6. Publish release

### Step 10: Add to Marketplace

If adding to your marketplace:

**1. Add to marketplace.json:**
```json
{
  "plugins": [
    {
      "name": "plugin-name",
      "description": "Brief description",
      "source": {
        "source": "url",
        "url": "https://github.com/username/plugin-name.git"
      },
      "homepage": "https://github.com/username/plugin-name"
    }
  ]
}
```

**2. Update marketplace README:**
```markdown
| [plugin-name](https://github.com/username/plugin-name) | Brief description |
```

**3. Test marketplace installation:**
```bash
/plugin marketplace update marketplace-name
/plugin install plugin-name@marketplace-name
```

## Best Practices

### Plugin Design

✅ **Do:**
- Start simple, add complexity as needed
- Focus on one problem domain
- Use clear, descriptive names
- Document everything thoroughly
- Test before publishing
- Follow semantic versioning
- Respect user privacy and security

❌ **Don't:**
- Build everything into one plugin
- Use generic names
- Skip documentation
- Hard-code secrets
- Forget to test
- Ignore licensing
- Add unnecessary complexity

### Component Guidelines

**Commands:**
- Action-oriented names (`sync-contacts`, not `contacts-sync`)
- Clear usage documentation
- Helpful error messages

**Skills:**
- Include trigger phrases in description
- Provide step-by-step processes
- Include examples

**Agents:**
- Define clear responsibilities
- Limit tools when possible
- Document expected inputs/outputs

**Hooks:**
- Keep fast (they run synchronously)
- Handle errors gracefully
- Document side effects

### Documentation

✅ **Do:**
- Write for beginners
- Include examples
- Document configuration
- Explain troubleshooting
- Keep updated

❌ **Don't:**
- Assume knowledge
- Skip examples
- Hide configuration requirements
- Ignore common issues
- Let docs get stale

## Common Patterns

### Simple Command Plugin

Just commands, no complexity:
```
plugin/
├── .claude-plugin/plugin.json
├── commands/
│   ├── command-1.md
│   └── command-2.md
└── README.md
```

### Integration Plugin

Wraps external service with MCP:
```
plugin/
├── .claude-plugin/plugin.json
├── .mcp.json
├── commands/sync.md
└── README.md
```

### Workflow Plugin

Skills-focused for processes:
```
plugin/
├── .claude-plugin/plugin.json
├── skills/
│   ├── workflow-1/SKILL.md
│   └── workflow-2/SKILL.md
└── README.md
```

### Wrapper Plugin

Curates external content:
```
plugin/
├── .claude-plugin/plugin.json
├── skills/              # Git submodule
├── commands/sync.md
└── README.md
```

## Resources

- [Plugin Development Guide](../../docs/plugin-development.md)
- [Skill Curation Guide](../../docs/skill-curation.md)
- [MCP Integration Guide](../../docs/mcp-integration.md)
- [Marketplace Management Guide](../../docs/marketplace-management.md)

## Next Steps

1. Define your plugin scope (Step 1)
2. Choose components needed (Step 2)
3. Create repository structure (Step 3)
4. Write plugin manifest (Step 4)
5. Implement components (Step 5)
6. Write documentation (Step 6)
7. Test thoroughly (Step 8)
8. Publish to GitHub (Step 9)
9. Add to marketplace (Step 10)
