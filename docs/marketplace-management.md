# Marketplace Management Guide

How to manage and maintain your Claude Code plugin marketplace.

## Table of Contents

1. [Marketplace Structure](#marketplace-structure)
2. [marketplace.json Format](#marketplacejson-format)
3. [Adding New Plugins](#adding-new-plugins)
4. [Versioning and Updates](#versioning-and-updates)
5. [Documentation Standards](#documentation-standards)
6. [Quality Guidelines](#quality-guidelines)
7. [Maintenance Workflows](#maintenance-workflows)

## Marketplace Structure

A Claude Code marketplace is a Git repository containing:

```
marketplace-repo/
├── .claude-plugin/
│   └── marketplace.json     # Marketplace configuration
├── docs/                    # Optional: Documentation
├── skills/                  # Optional: Marketplace-level skills
├── README.md                # Required: Marketplace documentation
└── LICENSE                  # Recommended: License file
```

### Key Concepts

**Marketplace Repository:** The repo containing marketplace.json (this repo)
**Plugin Repository:** Individual repos containing Claude Code plugins
**Source Types:** How plugins are referenced (URL, local path, etc.)

## marketplace.json Format

### Basic Structure

```json
{
  "$schema": "https://anthropic.com/claude-code/marketplace.schema.json",
  "name": "marketplace-name",
  "version": "1.0.0",
  "description": "Brief description of marketplace purpose",
  "owner": {
    "name": "Owner Name",
    "url": "https://github.com/username"
  },
  "plugins": []
}
```

### Field Reference

| Field | Required | Type | Description |
|-------|----------|------|-------------|
| `$schema` | ✅ Yes | string | Schema URL for validation |
| `name` | ✅ Yes | string | Marketplace identifier (kebab-case) |
| `version` | ✅ Yes | string | Semantic version |
| `description` | ✅ Yes | string | Marketplace description |
| `owner` | ✅ Yes | object | Owner information |
| `plugins` | ✅ Yes | array | List of plugin entries |

### Plugin Entry Format

```json
{
  "name": "plugin-name",
  "description": "Brief plugin description",
  "source": {
    "source": "url",
    "url": "https://github.com/user/plugin-name.git"
  },
  "homepage": "https://github.com/user/plugin-name"
}
```

### Plugin Entry Fields

| Field | Required | Type | Description |
|-------|----------|------|-------------|
| `name` | ✅ Yes | string | Plugin identifier (must match plugin.json) |
| `description` | ✅ Yes | string | Short plugin description |
| `source` | ✅ Yes | object | Source location configuration |
| `homepage` | ❌ No | string | Plugin homepage URL |
| `disabled` | ❌ No | boolean | Set true to temporarily disable |

### Source Types

**Git URL (most common):**
```json
{
  "source": {
    "source": "url",
    "url": "https://github.com/user/plugin-name.git"
  }
}
```

**Git with version:**
```json
{
  "source": {
    "source": "url",
    "url": "https://github.com/user/plugin-name.git",
    "ref": "v1.0.0"
  }
}
```

**Local path (for development):**
```json
{
  "source": {
    "source": "local",
    "path": "/path/to/plugin"
  }
}
```

## Adding New Plugins

### Pre-Flight Checklist

Before adding a plugin to your marketplace:

- [ ] Plugin has valid `.claude-plugin/plugin.json`
- [ ] Plugin is tested and working
- [ ] README includes installation instructions
- [ ] LICENSE file present
- [ ] Repository is public (or accessible to users)
- [ ] Plugin meets quality guidelines
- [ ] No security concerns or malware

### Step-by-Step Process

**1. Verify plugin structure:**
```bash
# Clone and test the plugin
git clone https://github.com/user/plugin-name.git
cd plugin-name
cat .claude-plugin/plugin.json  # Verify manifest

# Test locally
/plugin install .
```

**2. Add to marketplace.json:**
```json
{
  "plugins": [
    {
      "name": "existing-plugin",
      "description": "...",
      "source": { "source": "url", "url": "..." },
      "homepage": "..."
    },
    {
      "name": "new-plugin",
      "description": "Brief description of new plugin",
      "source": {
        "source": "url",
        "url": "https://github.com/user/new-plugin.git"
      },
      "homepage": "https://github.com/user/new-plugin"
    }
  ]
}
```

**3. Update marketplace README:**
```markdown
| Plugin | Description |
|--------|-------------|
| [existing-plugin](...) | ... |
| [new-plugin](https://github.com/user/new-plugin) | Brief description |
```

**4. Test marketplace update:**
```bash
# Commit changes
git add .claude-plugin/marketplace.json README.md
git commit -m "Add new-plugin to marketplace"
git push

# Test installation
/plugin marketplace update marketplace-name
/plugin install new-plugin@marketplace-name
```

**5. Verify installation:**
```bash
ls ~/.claude/plugins/new-plugin/
# Should show plugin files

# Test plugin functionality
# Try using commands, skills, or features
```

### Automation with Script

Create a helper script for adding plugins:

```bash
#!/bin/bash
# add-plugin.sh

PLUGIN_NAME=$1
PLUGIN_URL=$2
PLUGIN_DESC=$3

if [ -z "$PLUGIN_NAME" ] || [ -z "$PLUGIN_URL" ]; then
  echo "Usage: ./add-plugin.sh <name> <url> <description>"
  exit 1
fi

# Add to marketplace.json (requires jq)
jq --arg name "$PLUGIN_NAME" \
   --arg url "$PLUGIN_URL" \
   --arg desc "$PLUGIN_DESC" \
   '.plugins += [{
     "name": $name,
     "description": $desc,
     "source": {"source": "url", "url": $url},
     "homepage": $url
   }]' .claude-plugin/marketplace.json > tmp.json
mv tmp.json .claude-plugin/marketplace.json

# Add to README
echo "| [$PLUGIN_NAME]($PLUGIN_URL) | $PLUGIN_DESC |" >> README.md

echo "Added $PLUGIN_NAME to marketplace"
echo "Review changes and commit"
```

## Versioning and Updates

### Marketplace Versioning

Use semantic versioning for the marketplace:

```
1.0.0 - Initial marketplace release
1.1.0 - Added new plugin
1.2.0 - Added multiple plugins
2.0.0 - Breaking changes (restructure, removed plugins)
```

**When to bump version:**
- **Patch (1.0.X):** Documentation fixes, typo corrections
- **Minor (1.X.0):** New plugins added, non-breaking improvements
- **Major (X.0.0):** Plugins removed, breaking restructure

### Plugin Version Pinning

**Latest version (recommended for stable plugins):**
```json
{
  "source": {
    "source": "url",
    "url": "https://github.com/user/plugin.git"
  }
}
```

**Pinned to tag/release:**
```json
{
  "source": {
    "source": "url",
    "url": "https://github.com/user/plugin.git",
    "ref": "v1.0.0"
  }
}
```

**Pinned to branch:**
```json
{
  "source": {
    "source": "url",
    "url": "https://github.com/user/plugin.git",
    "ref": "stable"
  }
}
```

**When to pin versions:**
- Plugin has breaking changes frequently
- You want stable, tested version
- Plugin is experimental or beta

**When NOT to pin:**
- Plugin is stable and well-maintained
- You want users to get latest features/fixes
- Plugin follows semantic versioning

### Update Notifications

Document update policy in README:

```markdown
## Updates

This marketplace is updated regularly:
- **Weekly:** Review for new plugins
- **Monthly:** Update pinned versions
- **As needed:** Security fixes, critical updates

To update your installed plugins:
\`\`\`bash
/plugin marketplace update marketplace-name
/plugin update plugin-name
\`\`\`
```

## Documentation Standards

### Marketplace README Requirements

Every marketplace should include:

**1. Overview:**
```markdown
# Marketplace Name

Brief description of marketplace purpose and focus.

## Purpose

Who is this marketplace for and what problems does it solve?
```

**2. Plugin List:**
```markdown
## Plugins

| Plugin | Description |
|--------|-------------|
| [plugin-1](url) | Description |
| [plugin-2](url) | Description |
```

**3. Installation Instructions:**
```markdown
## Installation

1. Add marketplace to Claude Code:
   \`\`\`
   /plugin marketplace add owner/repo-name
   \`\`\`

2. Install a plugin:
   \`\`\`
   /plugin install plugin-name@marketplace-name
   \`\`\`
```

**4. Configuration (if needed):**
```markdown
## Configuration

Some plugins require environment variables. See individual plugin READMEs for setup.

Create \`.env\` file:
\`\`\`bash
PLUGIN_API_KEY=...
\`\`\`
```

**5. Additional Documentation:**
```markdown
## Documentation

- [Plugin Development](docs/plugin-development.md)
- [Skill Curation](docs/skill-curation.md)
- [MCP Integration](docs/mcp-integration.md)
```

### Plugin-Level Documentation

Each plugin in marketplace should have:

- **README.md:** Installation, usage, configuration
- **LICENSE:** Clear licensing information
- **CHANGELOG.md:** Version history and changes
- **Examples:** Usage examples and tutorials

## Quality Guidelines

### Acceptance Criteria

Plugins must meet these criteria to be added:

#### ✅ Required

- [ ] Valid `.claude-plugin/plugin.json` manifest
- [ ] README with installation and usage instructions
- [ ] LICENSE file (open source or clear terms)
- [ ] Tested and working
- [ ] No security vulnerabilities
- [ ] No malware or malicious code
- [ ] Repository is accessible
- [ ] Clear description and purpose

#### ✅ Recommended

- [ ] Semantic versioning
- [ ] CHANGELOG for version history
- [ ] Examples and tutorials
- [ ] Comprehensive error handling
- [ ] Performance tested
- [ ] Compatible with latest Claude Code
- [ ] Active maintenance (updated within 6 months)

### Review Process

**Before adding plugin:**

1. **Security review:**
   - No hard-coded credentials
   - No suspicious network requests
   - Dependencies are trustworthy
   - Hooks don't run dangerous commands

2. **Quality review:**
   - Code is readable and maintainable
   - Documentation is comprehensive
   - Error messages are helpful
   - No obvious bugs

3. **Compatibility review:**
   - Works with current Claude Code version
   - No conflicts with other marketplace plugins
   - Dependencies are available
   - Platform compatibility documented

4. **Licensing review:**
   - License is compatible with your marketplace
   - Attribution requirements are clear
   - Commercial use is allowed (if applicable)

### Removal Criteria

Remove plugins that:

- Contain security vulnerabilities (immediate)
- Are abandoned (>1 year no updates)
- Have broken functionality
- Violate licensing terms
- Receive negative user feedback
- No longer serve marketplace purpose

**Removal process:**
1. Document reason for removal
2. Notify plugin author (if possible)
3. Remove from marketplace.json
4. Update README
5. Add note in CHANGELOG
6. Bump marketplace version

## Maintenance Workflows

### Weekly Maintenance

**Tasks:**
- [ ] Check for new plugin submissions
- [ ] Review open issues/PRs
- [ ] Update documentation as needed
- [ ] Test critical plugins still work

**Time estimate:** 30-60 minutes

### Monthly Maintenance

**Tasks:**
- [ ] Review all plugin update logs
- [ ] Update pinned versions if needed
- [ ] Test all marketplace plugins
- [ ] Review and update documentation
- [ ] Check for abandoned plugins
- [ ] Update dependencies
- [ ] Publish marketplace updates

**Time estimate:** 2-4 hours

### Quarterly Maintenance

**Tasks:**
- [ ] Comprehensive security review
- [ ] Evaluate plugin quality and relevance
- [ ] Update quality guidelines
- [ ] Reach out to plugin authors
- [ ] Plan new features or improvements
- [ ] Review and update marketplace strategy

**Time estimate:** 4-8 hours

### Automation Opportunities

**GitHub Actions for:**

```yaml
# .github/workflows/validate.yml
name: Validate Marketplace

on:
  pull_request:
    paths:
      - '.claude-plugin/marketplace.json'

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Validate JSON
        run: |
          jq empty .claude-plugin/marketplace.json
      - name: Check plugin URLs
        run: |
          # Verify all plugin URLs are accessible
          jq -r '.plugins[].source.url' .claude-plugin/marketplace.json | \
          while read url; do
            git ls-remote "$url" > /dev/null || echo "Failed: $url"
          done
```

### Communication

**Changelog format:**
```markdown
# Changelog

## [1.2.0] - 2026-01-19

### Added
- vercel-skills plugin for React and design best practices
- letta-skills plugin for AI agent capabilities

### Changed
- Updated personal-crm to v2.0.0

### Removed
- deprecated-plugin (no longer maintained)
```

**User notifications:**
- GitHub releases for major updates
- README updates for new plugins
- Issue tracker for questions/support

## Best Practices

### Marketplace Organization

✅ **Do:**
- Group related plugins logically
- Maintain consistent naming conventions
- Document everything thoroughly
- Version semantically
- Test before publishing
- Monitor plugin health
- Respond to user feedback

❌ **Don't:**
- Add untested plugins
- Skip security reviews
- Ignore licensing
- Let documentation get stale
- Add too many similar plugins
- Forget to update CHANGELOG

### Plugin Curation

✅ **Do:**
- Focus on quality over quantity
- Establish clear acceptance criteria
- Review plugins before adding
- Monitor plugin maintenance
- Remove abandoned plugins
- Credit original authors
- Maintain high standards

❌ **Don't:**
- Add every plugin you find
- Skip quality reviews
- Ignore user complaints
- Keep broken plugins listed
- Forget to attribute sources

## Resources

- [Plugin Development Guide](./plugin-development.md)
- [Skill Curation Guide](./skill-curation.md)
- [MCP Integration Guide](./mcp-integration.md)
- [Claude Code Plugin Documentation](https://github.com/anthropics/claude-code/blob/main/plugins/README.md)

## Next Steps

1. Set up marketplace repository structure
2. Create initial marketplace.json
3. Add first plugin
4. Write comprehensive README
5. Set up maintenance schedule
6. Establish quality guidelines
7. Start curating valuable plugins
