# skill-guard 🛡️

> Security guard for Claude Code skill and plugin installations.

**skill-guard** intercepts every `claude plugin install` and `npx skills add` command and displays a risk report **before** (or during) installation — so you always know what you're installing.

## Risk levels

| Icon | Level | Condition |
|------|-------|-----------|
| ✅ | Safe | Safe + 0 alerts across all available scanners |
| ⚠️ | Medium risk | Medium Risk on at least one scanner |
| ❌ | High risk | High or Critical Risk on at least one scanner |
| ❓ | Unknown | No risk data available |

If ⚠️, ❌ or ❓ is detected, skill-guard **blocks the installation** and asks for explicit confirmation before continuing.

## How it works

### For `claude plugin install`
Runs `claude plugin details` before installing to inspect the plugin inventory:
hooks, MCP servers, bash scripts, unusual permissions. No external scanners
are available for this channel, so structural analysis is used instead.

### For `npx skills add`
Two-phase check:
1. **Pre-check** — WebFetch on `skills.sh/<org>/<repo>` to retrieve published risk scores
2. **Interception** — reads Socket/Snyk scores from the installation output before files are written to disk. If risk is detected at this stage, offers immediate rollback via `npx skills remove`

## Installation

```bash
npx skills add https://github.com/baalyerob/skill-guard
```

Or for all agents:
```bash
npx skills add https://github.com/baalyerob/skill-guard --all
```

## Example output

```
⚠️ find-skills
  Source    : https://github.com/vercel-labs/skills
  Scanners  : Gen=Safe · Socket=0 alerts · Snyk=Med Risk
  Détails   : https://skills.sh/vercel-labs/skills

A medium risk has been detected. Do you still want to install this package?
```

## Why this exists

The `npx skills add` and `claude plugin install` commands execute code with
**full agent permissions**. A risky skill can read your files, make network
requests, or exfiltrate data. skill-guard makes sure you see the risk report
before giving that permission.

## Author

Created by [@baalyerob](https://github.com/baalyerob) with Claude Code.

## License

MIT
