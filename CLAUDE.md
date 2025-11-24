# CLAUDE.md - AI Assistant Guide

This document provides comprehensive guidance for AI assistants working on the Awesome Claude Skills repository.

## Repository Overview

**awesome-claude-skills** is a curated marketplace of practical Claude Skills that extend Claude's capabilities across all platforms (Claude.ai, Claude Code, and Claude API). This repository serves both as:

1. A **catalog** of community-contributed skills (README.md)
2. A **collection** of official skills maintained directly in this repository

## Repository Structure

```
awesome-claude-skills/
├── .claude-plugin/
│   └── marketplace.json          # Skill marketplace metadata and registry
├── README.md                      # Main catalog of all skills (internal & external)
├── CONTRIBUTING.md                # Contribution guidelines
├── CLAUDE.md                      # This file - AI assistant guide
├── {skill-name}/                  # Individual skill directories
│   ├── SKILL.md                   # Required: skill instructions with YAML frontmatter
│   ├── LICENSE.txt                # Optional: skill-specific license
│   ├── scripts/                   # Optional: executable helper scripts
│   ├── references/                # Optional: documentation for context
│   └── assets/                    # Optional: templates, fonts, images
└── document-skills/               # Special collection of document processing skills
    ├── docx/
    ├── pdf/
    ├── pptx/
    └── xlsx/
```

## What Are Claude Skills?

Claude Skills are modular, self-contained packages that transform Claude from a general-purpose agent into a specialized agent equipped with:

- **Specialized workflows**: Multi-step procedures for specific domains
- **Tool integrations**: Instructions for working with specific file formats or APIs
- **Domain expertise**: Company-specific knowledge, schemas, business logic
- **Bundled resources**: Scripts, references, and assets for complex and repetitive tasks

### Progressive Disclosure Pattern

Skills use a three-level loading system to manage context efficiently:

1. **Metadata (name + description)** - Always in context (~100 words)
2. **SKILL.md body** - When skill triggers (<5k words)
3. **Bundled resources** - As needed by Claude (unlimited*)

*Unlimited because scripts can be executed without reading into context window.

## Core Conventions

### Skill Structure Requirements

Every skill MUST have:

```markdown
---
name: skill-name
description: One-sentence description of what this skill does and when to use it.
license: Complete terms in LICENSE.txt  # Optional but recommended
---

# Skill Name

[Detailed instructions for Claude...]
```

### Naming Conventions

- **Skill directory names**: lowercase with hyphens (e.g., `meeting-insights-analyzer`)
- **Skill names in YAML**: match directory name exactly
- **Description**: Use third-person (e.g., "This skill should be used when..." not "Use this skill when...")
- **Writing style**: Use imperative/infinitive form (verb-first instructions), not second person

### Skill Categories

Skills are organized into these categories:

- **business-marketing**: Lead generation, competitive research, branding, business development
- **communication-writing**: Content creation, meeting analysis, communication improvement
- **creative-media**: Images, videos, audio, creative content, design
- **development**: Software development, documentation, technical workflows, code tools
- **productivity-organization**: File organization, task management, personal productivity
- **collaboration-project-management**: Team workflows, project tracking
- **security-systems**: Security analysis, forensics, system administration
- **data-analysis**: Data processing, analysis, visualization

## Bundled Resources

### scripts/

Executable code (Python/Bash/etc.) for tasks requiring deterministic reliability or repeatedly rewritten code.

- **When to include**: Same code being rewritten repeatedly or deterministic reliability needed
- **Benefits**: Token efficient, deterministic, may be executed without loading into context
- **Example**: `scripts/rotate_pdf.py` for PDF rotation tasks

### references/

Documentation and reference material loaded as needed into context to inform Claude's process.

- **When to include**: Documentation Claude should reference while working
- **Examples**: Database schemas, API docs, company policies, workflow guides
- **Benefits**: Keeps SKILL.md lean, loaded only when Claude determines it's needed
- **Best practice**: If files are large (>10k words), include grep search patterns in SKILL.md
- **Avoid duplication**: Information should live in either SKILL.md or references/, not both

### assets/

Files not intended to be loaded into context, but rather used within the output Claude produces.

- **When to include**: Files that will be used in the final output
- **Examples**: Templates, images, icons, boilerplate code, fonts, sample documents
- **Use cases**: Files that get copied, modified, or embedded in output
- **Benefits**: Separates output resources from documentation

## Development Workflows

### Adding a New Skill

1. **Understand the use case** with concrete examples
2. **Plan reusable contents** (scripts, references, assets)
3. **Create skill directory** following naming conventions
4. **Write SKILL.md** with proper YAML frontmatter
5. **Add bundled resources** if needed
6. **Update marketplace.json** in `.claude-plugin/` directory
7. **Update README.md** in the appropriate category (alphabetical order)
8. **Test across platforms** (Claude.ai, Claude Code, API)
9. **Submit pull request** following contribution guidelines

### Updating an Existing Skill

1. **Read the existing SKILL.md** to understand current implementation
2. **Identify specific improvements** based on user feedback or testing
3. **Maintain consistency** with existing structure and style
4. **Update version** in marketplace.json if applicable
5. **Test changes** across platforms
6. **Document changes** in pull request description

### Modifying README.md

When adding/updating skills in README.md:

- Add skills in **alphabetical order** within their category
- Follow this exact format: `- [Skill Name](./skill-name/) - One-sentence description. *By [@username](github-url)* (for external contributions)`
- **No emojis**, consistent punctuation
- Include attribution for external skills
- Keep descriptions concise and clear

### Working with marketplace.json

The `.claude-plugin/marketplace.json` file is the registry for skills in this marketplace:

```json
{
  "plugins": [
    {
      "name": "skill-name",
      "description": "Description matching SKILL.md frontmatter",
      "source": "./skill-name",
      "category": "development"
    }
  ]
}
```

Always keep this file synchronized with:
- Skill directory structure
- SKILL.md frontmatter
- README.md listings

## Testing Guidelines

### Before Submitting

Skills MUST be tested to ensure:

1. **Functional correctness**: Skill performs intended tasks correctly
2. **Clear triggers**: Description accurately reflects when skill should activate
3. **Context efficiency**: Uses progressive disclosure appropriately
4. **Resource validity**: Scripts execute, references are accurate, assets load correctly
5. **Cross-platform compatibility**: Works in Claude.ai, Claude Code, and/or API
6. **No destructive operations**: Confirms before any file deletion or system changes
7. **Security**: No vulnerabilities introduced, safe execution

### Common Issues to Check

- YAML frontmatter is properly formatted
- Skill name matches directory name
- Description is clear and specific
- File paths in SKILL.md are correct
- Scripts have proper permissions and dependencies documented
- References are organized and not duplicated in SKILL.md
- Assets are included and properly referenced

## Git Workflow

### Branch Strategy

- **Main branch**: Production-ready skills only
- **Feature branches**: Use format `add-skill-name` or `update-skill-name`
- **Current development branch**: `claude/claude-md-micy49eociv853wu-011NxwStmWezfEa34hfYtJfH`

### Commit Guidelines

- Use clear, descriptive commit messages
- Format: `Add [Skill Name] skill` or `Update [Skill Name] skill`
- Keep commits focused on single logical changes
- Reference issues/PRs when applicable

### Pull Request Requirements

Your PR should include:

- **Clear title**: "Add [Skill Name] skill" or "Update [Skill Name] skill"
- **Description** explaining:
  - What problem it solves
  - Who uses this workflow
  - Attribution/inspiration source
  - Example of how it's used
- **Changes to files**:
  - New/updated skill directory with SKILL.md
  - Updated marketplace.json entry
  - Updated README.md with skill listing
  - Any bundled resources (scripts/, references/, assets/)

## Quality Standards

### Skill Quality Checklist

All skills must:

1. ✅ **Solve a real problem** - Based on actual usage, not theoretical applications
2. ✅ **Be well-documented** - Clear instructions, examples, and use cases
3. ✅ **Be accessible** - Written for the appropriate audience
4. ✅ **Include examples** - Show practical, real-world usage
5. ✅ **Be tested** - Verify works across Claude platforms
6. ✅ **Be safe** - Confirm before destructive operations
7. ✅ **Be portable** - Work across Claude platforms when applicable

### Documentation Standards

- Write for Claude, not end users (skills are consumed by Claude)
- Include clear "When to Use This Skill" section
- Provide concrete examples and edge cases
- Document prerequisites and dependencies
- Include error handling guidance
- Keep SKILL.md under 5k words (use references/ for longer content)

### Code Standards

For scripts in `scripts/`:

- Include proper error handling
- Document dependencies and installation
- Use clear variable and function names
- Add comments for complex logic
- Make scripts executable and cross-platform when possible

## Common Patterns

### Document Processing Skills

Located in `document-skills/`, these follow a specialized pattern:

- Heavy use of references/ for format specifications
- Schema files for validation
- Scripts for deterministic operations (parsing, validation)
- Industry-standard conventions (e.g., financial model color coding for xlsx)

### Development Skills

Skills like `mcp-builder`, `webapp-testing`, `changelog-generator`:

- Often include template files in assets/
- Reference documentation for frameworks/tools
- Helper scripts for setup and validation
- Clear workflow steps

### Business Skills

Skills like `lead-research-assistant`, `competitive-ads-extractor`:

- Focus on workflows and research processes
- May include example templates
- Often integrate with external tools/APIs
- Clear output format specifications

## License Information

- **Repository license**: Apache License 2.0
- **Individual skills**: May have different licenses (check LICENSE.txt in skill directory)
- Always respect and maintain existing license files
- Include license attribution for external dependencies

## External References

### Official Documentation

- [Claude Skills Overview](https://www.anthropic.com/news/skills)
- [Skills User Guide](https://support.claude.com/en/articles/12512180-using-skills-in-claude)
- [Creating Custom Skills](https://support.claude.com/en/articles/12512198-creating-custom-skills)
- [Skills API Documentation](https://docs.claude.com/en/api/skills-guide)
- [Agent Skills Blog Post](https://anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)

### Community Resources

- [Anthropic Skills Repository](https://github.com/anthropics/skills) - Official example skills
- [Claude Community](https://community.anthropic.com) - Discussion forum
- [Skills Marketplace](https://claude.ai/marketplace) - Discover and share skills

## Working with This Repository

### As a Contributor

When adding or updating skills:

1. Read CONTRIBUTING.md thoroughly
2. Review existing skills in the same category for patterns
3. Follow the skill creation process in skill-creator/SKILL.md
4. Test extensively before submitting
5. Provide clear documentation and attribution

### As a Maintainer

When reviewing contributions:

1. Verify skill solves a real problem with concrete use cases
2. Check adherence to structure and naming conventions
3. Validate YAML frontmatter and marketplace.json entries
4. Ensure README.md is updated correctly (alphabetical, proper format)
5. Test skill functionality across platforms
6. Verify license compatibility
7. Check for security issues or destructive operations

### As Claude Code Assistant

When helping users work with this repository:

1. **Always read existing files** before suggesting modifications
2. **Maintain consistency** with existing patterns and conventions
3. **Follow the skill creation process** from skill-creator/SKILL.md
4. **Use TodoWrite** for multi-step skill creation or updates
5. **Test changes** by reading the generated files
6. **Update all related files** (SKILL.md, marketplace.json, README.md)
7. **Respect licenses** and maintain proper attribution
8. **Be specific about categories** when organizing skills

## Troubleshooting

### Common Issues

**Skill not activating:**
- Check description clarity and specificity
- Verify YAML frontmatter is properly formatted
- Ensure skill name matches directory name

**Context window issues:**
- Move detailed documentation from SKILL.md to references/
- Use scripts/ for code instead of embedding in SKILL.md
- Add grep patterns for large reference files

**Cross-platform incompatibility:**
- Test on all supported platforms
- Document platform-specific requirements
- Use portable code patterns

**Marketplace registration:**
- Verify marketplace.json syntax
- Check category names match allowed values
- Ensure paths are correct relative to repository root

## Getting Help

- **Issues**: Open GitHub issue for bugs or questions
- **Discussions**: Use GitHub Discussions for design questions
- **Community**: Join Claude Community or Discord for support
- **Composio**: For integration with 500+ apps, see [Composio platform](https://platform.composio.dev/)

---

**Last Updated**: 2025-11-24
**Repository**: https://github.com/ComposioHQ/awesome-claude-skills
**Maintainer**: ComposioHQ (tech@composio.dev)
