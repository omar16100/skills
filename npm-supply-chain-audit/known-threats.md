# Known npm Supply Chain Threats

Last updated: 2026-04-01

## Axios Supply Chain Attack (2026-03-31)

**Attribution:** UNC1069 (North Korea) — Google GTIG
**Attack vector:** Compromised npm maintainer account (`jasonsaayman`)
**Exposure window:** ~3 hours (March 31, 00:21–03:25 UTC)
**Malware family:** WAVESHAPER.V2 (cross-platform RAT)

### Malicious Packages

| Package | Version | Published | Role |
|---------|---------|-----------|------|
| `axios` | `1.14.1` | 2026-03-31 00:21 UTC | Compromised — depends on plain-crypto-js |
| `axios` | `0.30.4` | 2026-03-31 01:00 UTC | Compromised — depends on plain-crypto-js |
| `plain-crypto-js` | `4.2.0` | 2026-03-30 05:57 UTC | Clean decoy to establish package history |
| `plain-crypto-js` | `4.2.1` | 2026-03-30 23:59 UTC | RAT dropper via postinstall script |
| `@shadanai/openclaw` | `2026.3.31-1` | 2026-03-31 | Related malicious package |
| `@shadanai/openclaw` | `2026.3.31-2` | 2026-03-31 | Related malicious package |
| `@qqbrowser/openclaw-qbot` | `0.0.130` | 2026-03-31 | Related malicious package |

### Safe Versions (downgrade targets)
- `axios@1.14.0` (latest safe 1.x)
- `axios@0.30.3` (latest safe 0.x)

### IOCs

**Network:**
- C2: `sfrclak[.]com:8000` (IP: `142.11.206.73`)
- Beacon URL: `http://sfrclak[.]com:8000/6202033`
- POST paths: `packages.npm.org/product0` (macOS), `product1` (Win), `product2` (Linux)
- User-Agent: `mozilla/4.0 (compatible; msie 8.0; windows nt 5.1; trident/4.0)`

**Filesystem:**
- macOS: `/Library/Caches/com.apple.act.mond`
- Windows: `%PROGRAMDATA%\wt.exe`, `%TEMP%\6202033.vbs`, `%TEMP%\6202033.ps1`
- Linux: `/tmp/ld.py`

**Payload hashes (SHA-256):**
- macOS: `92ff08773995ebc8d55ec4b8e1a225d0d1e51efa4ef88b8849d0071230c9645a`
- Windows PS: `617b67a8e1210e4fc87c92d1d1da45a2f311c08d26e89b12307cf583c900d101`
- Linux: `fcb81618bb15edfdedfb638b4c08a2af9cac9ecfa551af135a8402bf980375cf`

**Attacker accounts:**
- `nrwise` / `nrwise@proton.me` (published plain-crypto-js)
- `ifstap@proton.me` (set on compromised jasonsaayman account)

**Advisories:**
- Snyk: SNYK-JS-AXIOS-15850650, SNYK-JS-PLAINCRYPTOJS-15850652
- GitHub: axios/axios#10604

### Technical Details
- Obfuscation: Reversed Base64 + XOR cipher (key: `OrDeR_7077`, constant: 333)
- Dropper: `setup.js` (4,209 bytes) via npm postinstall hook
- Anti-forensics: Self-deletes setup.js, replaces package.json with clean decoy

---

## Crypto Library Typosquats (2026-03-24)

**Packages:** `raydium-bs58`, `base-x-64`, `base_xd`, `bs58-basic`, `ethersproject-wallet`
**Attack:** Silent private key exfiltration to Telegram bot
**Target:** Cryptocurrency developers

---

## Adding New Threats

When a new supply chain attack is discovered, add an entry with:
1. Attribution (if known)
2. Attack vector
3. Exposure window
4. Malicious package names and versions
5. Safe downgrade versions
6. Network and filesystem IOCs
7. Payload hashes
8. Advisory IDs
