# Known npm Supply Chain Threats

Last updated: 2026-09-27 (all claims re-checked against the linked sources)

Every claim below links to the public report it comes from. Keep it that way when adding entries.

## Axios Supply Chain Attack (2026-03-31)

**Attribution:** UNC1069, "a financially motivated North Korea-nexus threat actor", per Google Threat Intelligence Group ([GTIG][gtig])
**Attack vector:** Compromised npm account of the lead axios maintainer (`jasonsaayman`), confirmed in the maintainer's own post-mortem ([axios#10636][postmortem], [StepSecurity][stepsecurity]). The account email was changed to an attacker-controlled address ([GTIG][gtig])
**Exposure window:** About 3 hours on 2026-03-31: 00:21 to 03:15 UTC per the axios post-mortem ([axios#10636][postmortem]); GTIG gives 00:21 to 03:20 UTC ([GTIG][gtig])
**Malware family:** WAVESHAPER.V2 backdoor (RAT) for Windows, macOS and Linux, delivered by the SILKBELL dropper `setup.js` ([GTIG][gtig])

### Malicious Packages

| Package | Version | Published (UTC) | Role |
|---------|---------|-----------------|------|
| `axios` | `1.14.1` | 2026-03-31 00:21 | Compromised: adds `plain-crypto-js@4.2.1` as a dependency ([StepSecurity][stepsecurity]) |
| `axios` | `0.30.4` | 2026-03-31 01:00 | Compromised: same injection on the 0.x line ([StepSecurity][stepsecurity]) |
| `plain-crypto-js` | `4.2.0` | 2026-03-30 05:57 | Clean decoy to establish publishing history ([StepSecurity][stepsecurity]) |
| `plain-crypto-js` | `4.2.1` | 2026-03-30 23:59 | Dropper run by a `postinstall` hook ([GTIG][gtig], [StepSecurity][stepsecurity]) |
| `@shadanai/openclaw` | `2026.3.28-2`, `2026.3.28-3`, `2026.3.31-1`, `2026.3.31-2` | 2026-03-31 | In Socket's malicious package IOC list; Socket found the same dropper vendored in `2026.3.31-1` and `2026.3.31-2` ([Socket][socket]) |
| `@qqbrowser/openclaw-qbot` | `0.0.130` | 2026-03-31 | Ships a tampered `axios@1.14.1` with `plain-crypto-js` injected ([Socket][socket]) |

Publish times come from the npm registry `time` field, queried 2026-09-27 ([axios][npm-axios], [plain-crypto-js][npm-plaincrypto], [@shadanai/openclaw][npm-shadanai], [@qqbrowser/openclaw-qbot][npm-qqbrowser]); the axios and plain-crypto-js times also match the StepSecurity timeline ([StepSecurity][stepsecurity]). Socket assesses that the two openclaw packages were likely built while `axios@1.14.1` was the latest version and picked up the malicious dependency transitively ([Socket][socket]).

### Downgrade Targets

Last releases before the compromise, named in the axios post-mortem and by GTIG ([axios#10636][postmortem], [GTIG][gtig]):
- `axios@1.14.0` (1.x)
- `axios@0.30.3` (0.x)

### IOCs

**Network** ([GTIG][gtig]):
- C2: `sfrclak[.]com:8000` (IP: `142.11.206.73`)
- Beacon URL: `http://sfrclak[.]com:8000/6202033`
- POST bodies sent by the dropper: `packages.npm.org/product0` (macOS), `product1` (Windows), `product2` (Linux)
- User-Agent: `mozilla/4.0 (compatible; msie 8.0; windows nt 5.1; trident/4.0)`

**Filesystem:**
- macOS: `/Library/Caches/com.apple.act.mond` ([GTIG][gtig])
- Windows: `%PROGRAMDATA%\wt.exe`, `%TEMP%\6202033.ps1` ([GTIG][gtig]), `%TEMP%\6202033.vbs` ([StepSecurity][stepsecurity])
- Linux: `/tmp/ld.py` ([GTIG][gtig])

**Payload hashes (SHA-256)** ([GTIG][gtig]):
- macOS native binary: `92ff08773995ebc8d55ec4b8e1a225d0d1e51efa4ef88b8849d0071230c9645a`
- Windows stage 1: `617b67a8e1210e4fc87c92d1d1da45a2f311c08d26e89b12307cf583c900d101`
- Linux Python RAT: `fcb81618bb15edfdedfb638b4c08a2af9cac9ecfa551af135a8402bf980375cf`

**Attacker accounts:**
- `nrwise` / `nrwise@proton.me`: attacker-created account that published plain-crypto-js ([StepSecurity][stepsecurity], [Socket][socket])
- `ifstap@proton.me`: attacker email set on the compromised maintainer account ([GTIG][gtig], [StepSecurity][stepsecurity])

**Advisories:**
- Snyk: [SNYK-JS-AXIOS-15850650][snyk-axios], [SNYK-JS-PLAINCRYPTOJS-15850652][snyk-plaincrypto]
- GitHub: [axios/axios#10604][issue-10604] (community report), [axios/axios#10636][postmortem] (maintainer post-mortem)

### Technical Details
- Obfuscation: reversed Base64 plus XOR cipher (key: `OrDeR_7077`, constant: 333) ([StepSecurity][stepsecurity]); the key and constant also appear in GTIG's SILKBELL YARA rule ([GTIG][gtig])
- Dropper: `setup.js` (4209 bytes) run via the npm `postinstall` hook ([Socket][socket])
- Anti-forensics: deletes `setup.js` and replaces `package.json` with a clean stub stored as `package.md` ([GTIG][gtig], [StepSecurity][stepsecurity])

### Sources
- [GTIG: North Korea-Nexus Threat Actor Compromises Widely Used Axios NPM Package in Supply Chain Attack][gtig]
- [axios/axios#10636: Post Mortem: axios npm supply chain compromise][postmortem]
- [StepSecurity: axios Compromised on npm, Malicious Versions Drop Remote Access Trojan][stepsecurity]
- [Socket: Supply Chain Attack on Axios Pulls Malicious Dependency from npm][socket]
- [Snyk: SNYK-JS-AXIOS-15850650][snyk-axios]
- [Snyk: SNYK-JS-PLAINCRYPTOJS-15850652][snyk-plaincrypto]
- [axios/axios#10604: axios@1.14.1 and axios@0.30.4 are compromised][issue-10604]
- npm registry metadata: [axios][npm-axios], [plain-crypto-js][npm-plaincrypto], [@shadanai/openclaw][npm-shadanai], [@qqbrowser/openclaw-qbot][npm-qqbrowser]

---

## Crypto Library Typosquats (disclosed 2026-03-24)

**Packages:** `raydium-bs58`, `base-x-64`, `base_xd`, `bs58-basic`, `ethersproject-wallet`, all published by the npm account `galedonovan` ([Socket][socket-typosquats])
**Attack:** Sends private keys passed to Base58 `decode()` (Solana) or to the `Wallet` constructor of a cloned `@ethersproject/wallet` (Ethereum) to a hardcoded Telegram bot ([Socket][socket-typosquats])
**Target:** Solana and Ethereum developers ([Socket][socket-typosquats])

### Sources
- [Socket: 5 Malicious npm Packages Typosquat Solana and Ethereum Libraries to Steal Private Keys][socket-typosquats]

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
9. A source link for every claim above (drop any claim you cannot source)

[gtig]: https://cloud.google.com/blog/topics/threat-intelligence/north-korea-threat-actor-targets-axios-npm-package
[postmortem]: https://github.com/axios/axios/issues/10636
[stepsecurity]: https://www.stepsecurity.io/blog/axios-compromised-on-npm-malicious-versions-drop-remote-access-trojan
[socket]: https://socket.dev/blog/axios-npm-package-compromised
[snyk-axios]: https://security.snyk.io/vuln/SNYK-JS-AXIOS-15850650
[snyk-plaincrypto]: https://security.snyk.io/vuln/SNYK-JS-PLAINCRYPTOJS-15850652
[issue-10604]: https://github.com/axios/axios/issues/10604
[socket-typosquats]: https://socket.dev/blog/5-malicious-npm-packages-typosquat-solana-and-ethereum-libraries-steal-private-keys
[npm-axios]: https://registry.npmjs.org/axios
[npm-plaincrypto]: https://registry.npmjs.org/plain-crypto-js
[npm-shadanai]: https://registry.npmjs.org/@shadanai%2fopenclaw
[npm-qqbrowser]: https://registry.npmjs.org/@qqbrowser%2fopenclaw-qbot
