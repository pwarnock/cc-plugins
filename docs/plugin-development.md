# Plugin Development Guide

Comprehensive guide to creating Claude Code plugins.

## Table of Contents

1. [Plugin Structure](#plugin-structure)
2. [Plugin Manifest](#plugin-manifest)
3. [Plugin Components](#plugin-components)
4. [Best Practices](#best-practices)
5. [Testing and Validation](#testing-and-validation)

## Plugin Structure

Every Claude Code plugin follows this directory structure:

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json          # Required: Plugin manifest
├── .mcp.json                # Optional: MCP server configuration
├── commands/                # Optional: Slash commands
│   └── command-name.md
├── agents/                  # Optional: Specialized agents
│   └── agent-name.md
├── skills/                  # Optional: Skills (workflows, patterns)
│   └── skill-name/
│       └── SKILL.md
├── hooks/                   # Optional: Event hooks
│   └── hook-name.sh
├── README.md                # Recommended: Documentation
└── LICENSE                  # Recommended: License file
```

### Naming Conventions

- **Plugin name:** kebab-case (e.g., `personal-crm`, `vercel-skills`)
- **Commands:** kebab-case (e.g., `sync-skills`, `add-contact`)
- **Agents:** kebab-case (e.g., `code-reviewer`, `test-runner`)
- **Skills:** kebab-case directories (e.g., `create-plugin`, `test-driven-development`)
- **Hooks:** kebab-case with `.sh` extension (e.g., `pre-commit.sh`)

## Plugin Manifest

The `.claude-plugin/plugin.json` file is the only required file for a valid plugin.

### Minimal Manifest

```json
{
  "name": "plugin-name",
  "version": "1.0.0",
  "description": "Brief description of what the plugin does"
}
```

### Complete Manifest

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
  "keywords": ["keyword1", "keyword2", "category"],
  "dependencies": {
    "node": ">=18.0.0"
  }
}
```

### Manifest Fields

| Field | Required | Type | Description |
|-------|----------|------|-------------|
| `name` | ✅ Yes | string | Plugin name (kebab-case) |
| `version` | ✅ Yes | string | Semantic version (e.g., "1.0.0") |
| `description` | ✅ Yes | string | Brief plugin description |
| `author` | ❌ No | object | Author information |
| `homepage` | ❌ No | string | Plugin homepage URL |
| `repository` | ❌ No | string | Repository URL |
| `license` | ❌ No | string | License identifier (e.g., "MIT") |
| `keywords` | ❌ No | array | Search keywords |
| `dependencies` | ❌ No | object | System dependencies |

## Plugin Components

### 1. Commands (`commands/`)

Commands are user-invocable slash commands that appear in Claude Code.

**File format:** `commands/command-name.md`

**Structure:**
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

## Examples

...
```

**Best practices:**
- Use clear, action-oriented names (e.g., `sync-skills`, not `skills-sync`)
- Include usage examples
- Document required arguments and options
- Keep commands focused on a single task

### 2. Agents (`agents/`)

Agents are specialized sub-agents that Claude can invoke for specific tasks.

**File format:** `agents/agent-name.md`

**Structure:**
```markdown
---
name: agent-name
description: When to use this agent
tools: [tool1, tool2]  # Optional: limit available tools
color: blue            # Optional: UI color
---

# Agent System Prompt

Instructions for the agent's behavior and capabilities.

## When to Use

Describe the specific scenarios where this agent should be invoked.

## Capabilities

List what this agent can do.

## Examples

...
```

**Best practices:**
- Define clear trigger conditions
- Limit tools when possible (improves focus)
- Provide examples of typical usage
- Document expected inputs and outputs

### 3. Skills (`skills/`)

Skills are reusable workflows, patterns, or best practices.

**File format:** `skills/skill-name/SKILL.md`

**Structure:**
```markdown
---
name: skill-name
description: When and how to use this skill
---

# Skill Title

Detailed workflow or pattern description.

## When to Use

Describe scenarios where this skill applies.

## Process

1. Step one
2. Step two
3. Step three

## Examples

...
```

**Best practices:**
- Use descriptive, searchable names
- Include trigger phrases in description
- Provide step-by-step processes
- Include both rigid (TDD) and flexible (patterns) guidance

### 4. Hooks (`hooks/`)

Hooks are shell scripts that execute in response to events.

**File format:** `hooks/hook-name.sh`

**Available hooks:**
- `PreToolUse` - Before any tool is used
- `PostToolUse` - After any tool is used
- `Stop` - When conversation ends
- `SubagentStop` - When subagent completes
- `SessionStart` - When session begins
- `SessionEnd` - When session ends
- `UserPromptSubmit` - After user submits prompt
- `PreCompact` - Before conversation compaction
- `Notification` - When notification is received

**Structure:**
```bash
#!/bin/bash
# Hook script that responds to specific events

# Access event data via environment variables
echo "Hook triggered: $EVENT_NAME"

# Exit 0 for success, non-zero to block/fail
exit 0
```

**Best practices:**
- Keep hooks fast (they run synchronously)
- Use descriptive names
- Handle errors gracefully
- Document required environment variables

## Best Practices

### Plugin Organization

✅ **Do:**
- Keep plugins focused on a single domain or purpose
- Use clear, descriptive naming throughout
- Include README with installation and usage instructions
- Add LICENSE file for open source plugins
- Version using semantic versioning

❌ **Don't:**
- Mix unrelated functionality in one plugin
- Create deeply nested directory structures
- Include unnecessary dependencies
- Use generic names (e.g., "utils", "helpers")

### Documentation

✅ **Do:**
- Document all commands, agents, and skills
- Provide usage examples
- Explain configuration requirements
- Include troubleshooting guides

❌ **Don't:**
- Assume users know how to use your plugin
- Skip documenting environment variables
- Use jargon without explanation

### Component Design

✅ **Do:**
- Create components only when needed
- Follow single responsibility principle
- Make components composable
- Test components individually

❌ **Don't:**
- Create components "just in case"
- Duplicate functionality across components
- Make components too generic or too specific
- Skip testing

### Configuration

✅ **Do:**
- Use environment variables for secrets
- Provide sensible defaults
- Document all configuration options
- Validate configuration on load

❌ **Don't:**
- Hard-code credentials or API keys
- Require unnecessary configuration
- Use plugin-specific config files (prefer .env)

## Testing and Validation

### Local Testing

1. **Install locally:**
   ```bash
   cd /path/to/plugin
   /plugin install .
   ```

2. **Verify installation:**
   ```bash
   ls ~/.claude/plugins/plugin-name/
   ```

3. **Test components:**
   - Commands: Try invoking with `/command-name`
   - Skills: Ask Claude questions that should trigger the skill
   - Hooks: Verify hooks execute on appropriate events

### Validation Checklist

- [ ] Plugin manifest is valid JSON
- [ ] All required manifest fields present
- [ ] README includes installation instructions
- [ ] All commands have clear descriptions
- [ ] Skills have trigger phrases in descriptions
- [ ] Environment variables are documented
- [ ] Examples provided for all components
- [ ] No hard-coded secrets or credentials

### Common Issues

**Plugin not loading:**
- Check `.claude-plugin/plugin.json` is valid JSON
- Verify plugin name matches directory name
- Ensure version follows semantic versioning

**Commands not appearing:**
- Verify `commands/` directory exists
- Check command files have `.md` extension
- Ensure frontmatter has `name` and `description`

**Skills not triggering:**
- Add more descriptive trigger phrases
- Check skill name is clear and searchable
- Verify `SKILL.md` file is in skill directory

**Hooks not executing:**
- Ensure hook has execute permissions (`chmod +x`)
- Check hook name matches event name
- Verify hook exits with proper status code

## Example Plugins

### Minimal Plugin

```
minimal-plugin/
├── .claude-plugin/
│   └── plugin.json
└── README.md
```

### Full-Featured Plugin

```
full-plugin/
├── .claude-plugin/
│   └── plugin.json
├── .mcp.json
├── commands/
│   ├── command-1.md
│   └── command-2.md
├── agents/
│   └── specialized-agent.md
├── skills/
│   ├── skill-1/
│   │   └── SKILL.md
│   └── skill-2/
│       └── SKILL.md
├── hooks/
│   ├── pre-tool-use.sh
│   └── session-start.sh
├── README.md
├── LICENSE
└── .gitignore
```

## Resources

- [Claude Code Plugin Repository](https://github.com/anthropics/claude-code/blob/main/plugins/README.md)
- [MCP Integration Guide](./mcp-integration.md)
- [Marketplace Management Guide](./marketplace-management.md)
- [Skill Curation Guide](./skill-curation.md)

## Next Steps

1. Use the `create-plugin` skill to start a new plugin
2. Review existing plugins for patterns and examples
3. Test thoroughly before publishing
4. Add to marketplace once validated
