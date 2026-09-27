# Skills of Omar

Custom skills for [Claude Code](https://claude.ai/claude-code) CLI.

## Installation

```bash
# Install all skills via npx
npx skills add omar16100/skills-of-omar

# Or install manually via symlinks
git clone https://github.com/omar16100/skills-of-omar.git ~/skills-of-omar
mkdir -p ~/.claude/skills
ln -s ~/skills-of-omar/setup-google-analytics ~/.claude/skills/setup-google-analytics
ln -s ~/skills-of-omar/batik-checkin ~/.claude/skills/batik-checkin
ln -s ~/skills-of-omar/npm-supply-chain-audit ~/.claude/skills/npm-supply-chain-audit
```

## Available Skills

### setup-google-analytics

Sets up Google Analytics 4 for any website using Playwright browser automation.

**Usage:**
```
/setup-google-analytics example.com
```

**Requirements:**
- Playwright MCP server running
- Google account logged in (via Playwright browser)
- Website codebase accessible

**What it does:**
1. Navigates to Google Analytics
2. Creates new GA4 property
3. Sets up web data stream
4. Extracts measurement ID (G-XXXXXXXXXX)
5. Adds tracking code to website HTML
6. Optionally adds event tracking to JS

---

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

### npm-supply-chain-audit

Audits npm projects for malicious packages, supply chain attacks, typosquatting, and indicators of compromise (IOCs).

**Usage:**
```
/npm-supply-chain-audit axios@1.14.1         # Research specific package/version
/npm-supply-chain-audit plain-crypto-js      # Check a suspicious package
/npm-supply-chain-audit ~/my-project         # Audit a project's dependencies
/npm-supply-chain-audit                      # Audit current directory
```

**What it does:**
1. Researches package/version against security advisories (Snyk, Socket.dev, npm)
2. Scans lock files and node_modules for known malicious packages
3. Checks filesystem for RAT artifacts and C2 domains
4. Runs `npm audit` and flags suspicious postinstall scripts
5. Reports IOCs and provides remediation steps if compromised

Includes a living threat database (`known-threats.md`) with confirmed attacks (e.g., Axios/WAVESHAPER.V2 supply chain attack, March 2026).

---

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
