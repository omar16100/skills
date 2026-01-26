# Skills of Omar

Custom skills for [Claude Code](https://claude.ai/claude-code) CLI.

## Installation

```bash
# Clone the repo
git clone https://github.com/omarshab/skills-of-omar.git ~/skills-of-omar

# Create skills symlink directory
mkdir -p ~/.claude/skills

# Symlink individual skills
ln -s ~/skills-of-omar/batik-checkin ~/.claude/skills/batik-checkin
```

## Available Skills

### batik-checkin

Automates Batik Air Malaysia web check-in via BookCabin portal using Playwright browser automation.

**Usage:**
```
/batik-checkin BOOKING_REF
```

**Requirements:**
- Playwright MCP server running
- Valid booking reference
- Passenger passport details

**What it does:**
1. Navigates to BookCabin check-in portal
2. Enters booking reference
3. Fills APIS form (passport, DOB, nationality)
4. Fills emergency contact form
5. Handles seat selection dialogs
6. Accepts dangerous goods declaration
7. Downloads boarding pass

## Adding New Skills

Each skill is a directory containing a `SKILL.md` file with YAML frontmatter:

```yaml
---
name: my-skill
description: What the skill does
argument-hint: [optional-arg]
disable-model-invocation: true  # User-invoked only
allowed-tools: tool_pattern_*   # Restrict available tools
---

# Skill instructions here...
```

## License

MIT
