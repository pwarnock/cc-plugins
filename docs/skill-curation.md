# Skill Curation Guide

How to identify, evaluate, and package valuable skills for your Claude Code plugin marketplace.

## Table of Contents

1. [What is Skill Curation?](#what-is-skill-curation)
2. [Finding Valuable Skills](#finding-valuable-skills)
3. [Evaluating Skill Quality](#evaluating-skill-quality)
4. [Wrapping External Skills](#wrapping-external-skills)
5. [Maintaining Curated Collections](#maintaining-curated-collections)
6. [Case Study: Vercel Skills](#case-study-vercel-skills)

## What is Skill Curation?

Skill curation is the process of:
1. **Discovering** high-quality skills from various sources
2. **Evaluating** their relevance and quality
3. **Packaging** them as Claude Code plugins
4. **Maintaining** connections to upstream sources

Unlike creating original skills, curation focuses on making existing valuable content accessible in Claude Code format.

## Finding Valuable Skills

### Sources

**1. GitHub Repositories**
- Agent framework repositories (e.g., vercel-labs/agent-skills)
- Company-specific AI tool repositories
- Community skill collections
- Open source AI projects

**2. Documentation and Guides**
- Best practice documents
- Style guides and coding standards
- Framework-specific patterns
- Industry standards (WCAG, OWASP, etc.)

**3. Internal Knowledge**
- Team workflows and processes
- Company coding standards
- Domain-specific patterns
- Debugging procedures

### Search Strategies

**Keywords to search for:**
- "agent skills"
- "AI prompts"
- "Claude skills"
- "LLM workflows"
- "best practices"
- "[framework] patterns"

**Example searches:**
```
"react best practices" site:github.com
"debugging workflow" filetype:md
"design guidelines" organization:[company]
```

## Evaluating Skill Quality

### Quality Criteria

Use this checklist to evaluate potential skills:

#### ✅ High Quality Indicators

**Content Quality:**
- [ ] Clear, actionable guidelines
- [ ] Specific examples provided
- [ ] Well-structured and organized
- [ ] Regular updates/maintenance
- [ ] Comprehensive coverage of topic

**Relevance:**
- [ ] Applicable to your work
- [ ] Solves real problems
- [ ] Complements existing skills
- [ ] Not already covered by Claude's knowledge

**Usability:**
- [ ] Easy to understand
- [ ] Ready to use without modification
- [ ] Clear trigger conditions
- [ ] Appropriate scope (not too broad or narrow)

**Licensing:**
- [ ] Open source or permissive license
- [ ] Attribution requirements clear
- [ ] Commercial use allowed (if needed)

#### ❌ Red Flags

- Outdated information (>2 years old without updates)
- Vague or generic advice
- Contradicts current best practices
- Requires significant adaptation
- Unclear licensing or proprietary
- Overly complex or prescriptive

### Scoring System

Rate each skill on a 1-5 scale:

| Category | Weight | Score |
|----------|--------|-------|
| Content Quality | 30% | 1-5 |
| Relevance | 25% | 1-5 |
| Usability | 25% | 1-5 |
| Maintenance | 10% | 1-5 |
| Licensing | 10% | 1-5 |

**Decision threshold:**
- **4.0+**: Excellent candidate, include immediately
- **3.0-3.9**: Good candidate, consider after review
- **2.0-2.9**: Moderate quality, needs significant work
- **<2.0**: Not suitable for curation

### Example Evaluation

**Skill:** vercel-labs/agent-skills/react-best-practices

| Category | Score | Reasoning |
|----------|-------|-----------|
| Content | 5/5 | 40+ specific, actionable rules |
| Relevance | 5/5 | React/Next.js are core to work |
| Usability | 4/5 | Clear structure, minor adaptation needed |
| Maintenance | 5/5 | Active Vercel Labs repository |
| Licensing | 5/5 | MIT license, commercial-friendly |

**Total:** 4.8/5 ✅ Excellent candidate

## Wrapping External Skills

When external repositories aren't Claude Code plugins, create a wrapper plugin.

### Wrapper Plugin Pattern

#### Architecture

```
wrapper-plugin/
├── .claude-plugin/
│   └── plugin.json          # Wrapper plugin manifest
├── skills/                  # Git submodule → external repo
│   ├── skill-1/
│   ├── skill-2/
│   └── skill-3/
├── commands/
│   └── sync-skills.md       # Command to update from upstream
├── README.md                # Documentation and usage
└── LICENSE                  # License (note submodule licenses)
```

#### When to Use

**Use wrapper plugin when:**
- External repo is NOT a Claude Code plugin
- Want to maintain connection to upstream
- Curating multiple related skills
- Source repository is actively maintained

**Don't use wrapper when:**
- External repo is already a Claude Code plugin (reference directly)
- Skills need significant modification (fork instead)
- No upstream maintenance (copy and own)

### Creating a Wrapper Plugin

#### Step 1: Create Plugin Structure

```bash
mkdir -p wrapper-plugin/.claude-plugin
mkdir -p wrapper-plugin/commands
```

#### Step 2: Write Plugin Manifest

```json
{
  "name": "wrapper-plugin",
  "version": "0.1.0",
  "description": "Curated [category] skills from [source]",
  "author": {
    "name": "Your Name",
    "url": "https://github.com/yourusername"
  },
  "homepage": "https://github.com/yourusername/wrapper-plugin",
  "repository": "https://github.com/yourusername/wrapper-plugin",
  "license": "MIT"
}
```

#### Step 3: Add Git Submodule

```bash
cd wrapper-plugin
git init
git submodule add https://github.com/org/external-repo.git skills
```

**Why submodules?**
- Maintains connection to upstream
- Can pull updates easily
- Clear attribution
- Preserves external repo's git history

#### Step 4: Document the Skills

Create comprehensive README explaining:
- What skills are included
- How to use each skill
- Installation instructions
- How to update from upstream
- Credit to original source

#### Step 5: Add Sync Command

```markdown
---
name: sync-skills
description: Update skills from upstream repository
---

# Sync Skills

Updates skills to latest version from upstream.

\`\`\`bash
cd ~/.claude/plugins/wrapper-plugin
git submodule update --remote skills
\`\`\`
```

### Alternative: Transformation

If skills need adaptation, create transformation workflow:

#### 1. Fork the Repository

```bash
gh repo fork org/external-repo
```

#### 2. Transform to Claude Code Format

Convert external format to Claude Code skills structure:

```bash
# Example: Convert markdown guides to SKILL.md format
for file in guides/*.md; do
  mkdir -p skills/$(basename $file .md)
  # Add frontmatter and convert
  cat > skills/$(basename $file .md)/SKILL.md <<EOF
---
name: $(basename $file .md)
description: [Generated from $file]
---

$(cat $file)
EOF
done
```

#### 3. Maintain as Fork

- Keep fork synced with upstream
- Apply transformations in separate branch
- Document transformation process

## Maintaining Curated Collections

### Update Strategy

**Regular Updates:**
1. Schedule monthly review of upstream sources
2. Pull latest changes from submodules
3. Test updated skills
4. Increment plugin version
5. Document changes in CHANGELOG

**Handling Breaking Changes:**
- Test thoroughly before updating
- Document migration steps for users
- Consider pinning to specific versions if needed
- Provide rollback instructions

### Version Management

**Wrapper Plugin Versioning:**
```
0.1.0 - Initial curation
0.2.0 - Added new skill
1.0.0 - Stable, well-tested collection
1.1.0 - Updated skills from upstream
2.0.0 - Breaking changes in curated skills
```

**Submodule Pinning:**
```bash
# Pin to specific commit
cd skills
git checkout abc123
cd ..
git add skills
git commit -m "Pin skills to stable version"

# Update to latest
cd skills
git checkout main
git pull
cd ..
git add skills
git commit -m "Update skills to latest"
```

### Quality Monitoring

**Track metrics:**
- Upstream activity (commits, issues)
- User feedback on skills
- Skill usage patterns
- Bug reports

**Retirement criteria:**
- Upstream abandoned (>1 year no updates)
- Superseded by better skills
- No longer relevant to use cases
- Licensing changes

## Case Study: Vercel Skills

### Background

**Source:** [vercel-labs/agent-skills](https://github.com/vercel-labs/agent-skills)

**Challenge:** Valuable skills but not in Claude Code plugin format

**Solution:** Create wrapper plugin with git submodule

### Evaluation

| Skill | Quality | Rationale |
|-------|---------|-----------|
| react-best-practices | 5/5 | 40+ specific React/Next.js rules, actively maintained |
| web-design-guidelines | 5/5 | 100+ accessibility and UX rules, comprehensive |
| vercel-deploy-claimable | 4/5 | Direct Vercel integration, niche but valuable |

**Decision:** Include all three skills in single wrapper plugin

### Implementation

```
vercel-skills/
├── .claude-plugin/
│   └── plugin.json
├── skills/                  # Submodule → vercel-labs/agent-skills
│   ├── react-best-practices/
│   ├── web-design-guidelines/
│   └── vercel-deploy-claimable/
├── commands/
│   └── sync-skills.md
└── README.md
```

### Key Decisions

**Why wrapper instead of direct reference?**
- Vercel repo isn't a Claude Code plugin
- Adds proper plugin manifest and documentation
- Can add commands (like sync-skills)
- Provides controlled update mechanism

**Why include all three skills?**
- Related domain (Vercel development)
- Complementary functionality
- Easier maintenance as single plugin
- Clearer for users (one install)

**Why git submodule?**
- Maintains connection to active upstream
- Easy to pull updates
- Clear attribution
- Can contribute back to Vercel

### Maintenance Plan

**Monthly:**
- Check Vercel repo for updates
- Pull latest changes
- Test skills in real usage
- Update wrapper version

**Quarterly:**
- Review skill usage metrics
- Gather user feedback
- Consider adding/removing skills
- Update documentation

## Best Practices Summary

### ✅ Do

- Evaluate skills objectively using criteria
- Maintain connection to upstream sources
- Document thoroughly (usage, updates, credits)
- Test curated skills before publishing
- Version wrapper plugins semantically
- Monitor upstream for updates
- Respect original licenses and attribution

### ❌ Don't

- Curate low-quality or outdated skills
- Modify external content without forking
- Mix unrelated skills in one wrapper
- Forget to credit original sources
- Skip testing after updates
- Pin to old versions indefinitely
- Ignore licensing requirements

## Resources

- [Plugin Development Guide](./plugin-development.md)
- [Marketplace Management Guide](./marketplace-management.md)
- [Git Submodules Documentation](https://git-scm.com/book/en/v2/Git-Tools-Submodules)

## Next Steps

1. Identify candidate skills using search strategies
2. Evaluate using quality criteria
3. Create wrapper plugin structure
4. Add as git submodule
5. Document thoroughly
6. Test with real usage
7. Add to marketplace
8. Establish update schedule
