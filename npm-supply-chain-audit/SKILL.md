---
name: npm-supply-chain-audit
description: Audit npm projects for malicious packages, supply chain attacks, typosquatting, and IOCs. Use when user wants to check for compromised dependencies, suspicious packages, or verify project security.
argument-hint: "[package-name-or-version] or [project-path]"
---

# npm Supply Chain Security Audit

Perform a thorough security audit of npm dependencies, checking for malicious packages, compromised versions, typosquatting, and indicators of compromise (IOCs).

## Input Handling

- If `$ARGUMENTS` contains a **package name or version** (e.g., `axios@1.14.1`, `plain-crypto-js`): research that specific package/version for known attacks
- If `$ARGUMENTS` contains a **project path**: audit that project's dependencies
- If `$ARGUMENTS` is empty: audit the current working directory

## Phase 1: Package Research (if specific package/version given)

Use web search to investigate:

1. **Registry check**: Does this exact version exist on npm? Was it published and yanked?
2. **Security advisories**: Search Snyk, Socket.dev, GitHub Advisories, npm advisories for CVEs or reports
3. **Typosquatting analysis**: Is the package name suspiciously similar to a legitimate package?
4. **Publisher analysis**: Who published it? Is the publisher account suspicious (new, ProtonMail, no other packages)?
5. **Attack timeline**: If malicious, determine exposure window and attack vector

Report findings in a structured table format.

## Phase 2: Local Dependency Scan

Search the target project (the current working directory if no path given) for:

### 2a. Lock File & Manifest Search
- Search all `package.json` files for the target package in dependencies, devDependencies, optionalDependencies, peerDependencies
- Search all `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml` for resolved versions
- Search `node_modules/` directories for installed copies

### 2b. Known Malicious Package Database
Always check for these **confirmed malicious packages** in addition to any user-specified targets:

**Axios Supply Chain Attack (March 31, 2026)** (sources for every item are in `known-threats.md`):
- `axios@1.14.1`: compromised via hijacked maintainer account
- `axios@0.30.4`: compromised via hijacked maintainer account
- `plain-crypto-js@4.2.0`: clean decoy version, still suspicious
- `plain-crypto-js@4.2.1`: dropper for the WAVESHAPER.V2 RAT
- `@shadanai/openclaw@2026.3.28-2`: related malicious package
- `@shadanai/openclaw@2026.3.28-3`: related malicious package
- `@shadanai/openclaw@2026.3.31-1`: related malicious package
- `@shadanai/openclaw@2026.3.31-2`: related malicious package
- `@qqbrowser/openclaw-qbot@0.0.130`: related malicious package

**Common typosquatting targets:**
- Variations of `crypto-js` (e.g., `plain-crypto-js`, `crypto-jss`, `crypt-js`)
- Variations of `axios` with unusual version numbers
- Any package with a `postinstall` script that downloads or executes remote code

### 2c. Source Code Search
- Search for import/require statements referencing suspicious packages
- Search for C2 domains in any project files
- Search for obfuscated code patterns (reversed Base64, XOR cipher references)

## Phase 3: Filesystem IOC Check

Check for known RAT artifacts on the local machine:

### macOS
- `/Library/Caches/com.apple.act.mond`: WAVESHAPER.V2 macOS payload
- Check for suspicious LaunchAgents/LaunchDaemons

### Linux
- `/tmp/ld.py`: WAVESHAPER.V2 Python RAT

### Windows
- `%PROGRAMDATA%\wt.exe`: copy of PowerShell named like Windows Terminal
- `%TEMP%\6202033.vbs`: VBScript dropper
- `%TEMP%\6202033.ps1`: PowerShell payload

### Network IOCs
Search project files for known C2 domains:
- `sfrclak[.]com` (defang when reporting, search for `sfrclak`)
- IP `142.11.206.73`

## Phase 4: Vulnerability Scan

If a project path is identified:

1. Run `npm audit` (or `pnpm audit` / `yarn audit`) if available
2. Check for packages with `postinstall` scripts: `grep -r '"postinstall"' node_modules/*/package.json`
3. Flag any postinstall scripts that use `curl`, `wget`, `fetch`, `osascript`, `powershell`, or `python`

## Output Format

### Summary Table
| Check | Status | Details |
|-------|--------|---------|
| Malicious packages in deps | CLEAN/FOUND | list if found |
| Filesystem IOCs | CLEAN/FOUND | list if found |
| C2 domains in files | CLEAN/FOUND | list if found |
| Suspicious postinstall scripts | CLEAN/FOUND | list if found |
| npm audit vulnerabilities | X critical, Y high | counts |

### If Compromised: Remediation Steps
1. Remove malicious packages from `node_modules/` and lock files
2. Downgrade to last known safe version
3. Check for filesystem artifacts
4. Rotate ALL credentials on affected systems (npm tokens, cloud keys, SSH keys, CI/CD secrets)
5. If RAT artifacts found: isolate machine, rebuild from clean image

### Preventive Recommendations
- Pin exact versions (no `^` or `~` for critical deps)
- Use `npm ci --ignore-scripts` in CI/CD
- Commit lockfiles to version control
- Use Socket.dev, Snyk, or similar for real-time monitoring
- Enable npm 2FA on all publisher accounts

## Execution Guidelines

- Use **parallel** Explore agents for web research and local scanning simultaneously
- Use **Grep** for local file searches (not bash grep)
- **Defang** malicious URLs/domains in output (e.g., `sfrclak[.]com`)
- Always report the **exposure window** for time-bound attacks
- Include **attribution** if known (threat actor group, nation-state)
- Link to **sources** (Snyk advisories, Socket.dev reports, GitHub issues)
