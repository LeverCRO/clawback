# 🦞 ClawBack

**Crypto scam detection skills for OpenClaw.**

7 skills. Zero tolerance. Multi-chain.

→ **Website**: [clawback.bot](https://clawback.bot)
→ **Install**: `git clone https://github.com/LeverCRO/clawback.git`
→ **Or give your agent**: `clawback.bot/SKILL.md`

---

## Why I Built This

$17.2 billion lost to crypto scams in 2025 (Chainalysis). Not by idiots. By smart people who didn't pause long enough to name the trick being played on them.

I build AI agents. I live in the agentic economy. One of my agents approved a token swap on a contract that looked clean — verified source, renounced ownership, locked liquidity. Three days later the deployer reclaimed ownership through a backdoor function and drained every wallet that had approved. My agent didn't lose money because it was running in test mode. But someone else's agent — or someone else — would have.

That's when I realised: AI agents are about to operate in crypto autonomously — buying tokens, approving contracts, interacting with DeFi. They need a security layer that understands how scams actually work. Not just pattern matching. Not just blacklists. They need to understand the *psychology* behind the attack, the *on-chain mechanics* that enable it, and the *tells* that give it away before damage is done.

So I built ClawBack. Seven skills that work for both humans and their agents:

**For humans** — paste a suspicious message and ClawBack names the manipulation technique. Check a token before buying. Scan a link before clicking. Learn how scams work so you stop falling for them.

**For autonomous agents** — ClawBack becomes a security middleware. A trading agent runs token-check before every swap. A DeFi agent runs url-scan before visiting any dApp. Watchdog monitors wallets via cron and alerts only when threats appear. Your agent gets the instincts of a security researcher, running 24/7.

I scanned Vitalik Buterin's wallet as a test. ClawBack found four honeypot airdrops in one pass — including a token called "what the dog doing?" with a 97% sell tax. That token is sitting in thousands of wallets right now. Most of those people don't know it's there. The ones who try to interact with it will lose their real tokens.

```
🚨 WATCHDOG ALERT — 1 of 4

WALLET: 0xd8dA...96045 (vitalik.eth)
CHAIN:  Ethereum (1)
THREAT: Airdrop token with 97% sell tax

Token "what the dog doing?" appeared in wallet
without being purchased.

GoPlus flags:
  Sell tax: 96.93% — selling is impossible
  Blacklist: present — creator can freeze your wallet

ACTION: Do NOT interact with this token.
```

ClawBack is built for the [OpenClaw — Binance Social Contest](https://www.binance.com). But the problem it solves isn't a competition entry. It's a $17 billion problem that's growing every year. And the AI agents we're all building are about to make it worse — unless we give them the tools to make it better.

*— [Vance](https://x.com/LeverCRO)*

---

## The Suite

### Learn

| Skill | What It Does | Who It's For |
|---|---|---|
| [📖 clawback-learn](./clawback-learn/) | 30 key terms, 12 scam playbooks, security fundamentals in plain English | Humans learning crypto security |
| [🎯 clawback-train](./clawback-train/) | Interactive scam detection quizzes (14 scenarios), security setup walkthroughs | Humans practising threat detection |

### Protect

| Skill | What It Does | Who It's For |
|---|---|---|
| [👁️ clawback-watchdog](./clawback-watchdog/) | Cron-based wallet monitoring — catches poisoned airdrops, stale approvals, flagged addresses | Autonomous agents running 24/7 |

### Detect

| Skill | What It Does | Who It's For |
|---|---|---|
| [🛡️ clawback-analyse](./clawback-analyse/) | Social engineering detection — Cialdini psychological analysis + crypto attack vector mapping | Humans checking messages; agents screening inbound comms |
| [🔬 clawback-token-check](./clawback-token-check/) | On-chain scanner — honeypots, mint functions, rug pull indicators, holder concentration | Humans before buying; agents before swapping |
| [🔗 clawback-url-scan](./clawback-url-scan/) | URL/domain intelligence — VirusTotal + WHOIS + DNS + HTTP live checks | Humans before clicking; agents before visiting dApps |

### Respond

| Skill | What It Does | Who It's For |
|---|---|---|
| [📋 clawback-report](./clawback-report/) | Incident reports for law enforcement, community threat intelligence via Chainabuse | Humans documenting losses; agents compiling threat digests |

### Meta

| Skill | What It Does |
|---|---|
| [🦞 clawback-suite](./clawback-suite/) | Single install entry point — routes `/clawback` commands to the right skill |

---

## Quick Start

```bash
# Install the full suite
git clone https://github.com/LeverCRO/clawback.git
cp -r clawback/clawback-* ~/.openclaw/skills/

# Or install a single skill
# Copy just the skill folder you need into ~/.openclaw/skills/
```

Or just give your agent a URL:
```
"Read clawback.bot/SKILL.md before analysing this message"
```

### For humans — use ClawBack directly

**Got a suspicious message?**
```
/clawback-analyse
```
Paste the message. ClawBack identifies the psychological manipulation techniques, maps the attack vector, and tells you exactly what the scammer wants.

**About to buy a token?**
```
/clawback-token-check 0xTOKEN_ADDRESS
```
Auto-detects the chain. Checks for honeypots, hidden mint functions, unlocked liquidity, holder concentration, and deployer reputation.

**Want to check a link?**
```
/clawback-url-scan binance-secure-verify.com
```
Runs live WHOIS, DNS, HTTP, and VirusTotal checks. Catches domains registered days ago, keyword-stuffed URLs, and known phishing patterns.

**Want 24/7 monitoring?**
```
/clawback-watchdog 0xYOUR_WALLET
```
Asks which chain, then monitors every 8 hours. Only alerts when something needs your attention.

### For autonomous agents — use ClawBack as a security layer

Configure your agent to call ClawBack before risky operations:

```
Before any token swap → run /clawback-token-check [contract]
Before visiting any URL → run /clawback-url-scan [url]
Before acting on any message → run /clawback-analyse [message]
On a schedule → /clawback-watchdog monitors registered wallets
```

An agent with ClawBack installed won't approve a honeypot contract, won't visit a phishing domain, and won't act on a social engineering message — because it checks first. Every time. Without being asked.

---

## How It Works

```
LEARN → PROTECT → DETECT → RESPOND → repeat.
```

**Learn** the threats before they find you (`/clawback-learn`). **Protect** your wallet with always-on monitoring (`/clawback-watchdog`). **Detect** attacks when they target you (`/clawback-analyse`, `/clawback-token-check`, `/clawback-url-scan`). **Respond** with formal reports and community submissions (`/clawback-report`). **Train** to stay sharp (`/clawback-train`).

Every skill cross-references the others. An analyse verdict suggests running url-scan on embedded links. A token-check result suggests running analyse on the message that recommended the token. Watchdog alerts link to token-check for deeper analysis. The suite works as a system, not a collection.

---

## API Setup Guide

ClawBack works out of the box with zero API keys. Five of seven skills need nothing at all. Adding free API keys unlocks the full capabilities.

### What works without any keys

| Skill | Zero-config capabilities |
|---|---|
| **clawback-analyse** | Full functionality — pure LLM reasoning, no APIs |
| **clawback-learn** | Full functionality — pure LLM, no APIs |
| **clawback-train** | Full functionality — pure LLM, no APIs |
| **clawback-token-check** | Full functionality — GoPlus Security requires no key |
| **clawback-url-scan** | Level 1: known domain matching, WHOIS, DNS, HTTP live checks |
| **clawback-watchdog** | Check 1 (approval audit) + Check 3 (address reputation) via GoPlus |
| **clawback-report** | Incident report generation — no APIs needed |
| **clawback-suite** | Full functionality — just a router |

### Optional API keys — all free to obtain

#### VirusTotal → unlocks url-scan Level 2

Adds 90+ security engine scanning, domain reputation scores, and certificate analysis.

1. Sign up at [virustotal.com](https://www.virustotal.com)
2. Go to your profile → API Key
3. Free tier: 4 requests/minute, 500/day — more than enough

**Easiest setup** — via the OpenClaw Control UI:
- Open `http://127.0.0.1:18789/skills`
- Find **clawback-url-scan**
- Paste your key in the API key field → Click **Save key**

Or via terminal:
```bash
openclaw config set skills.entries.clawback-url-scan.apiKey "your-virustotal-key"
```

#### Etherscan → unlocks watchdog airdrop detection

Enables Check 2 — catches honeypot tokens airdropped to your wallet (like the "what the dog doing?" 97% sell tax token found in Vitalik's wallet).

1. Sign up at [etherscan.io](https://etherscan.io)
2. Go to API Keys → Create New Key
3. Free tier covers: **Ethereum, Polygon, Arbitrum** — full 3-check monitoring
4. Note: BSC requires Etherscan Pro (paid) — GoPlus checks still work on BSC without it

**Easiest setup** — via the Control UI:
- Open `http://127.0.0.1:18789/skills`
- Find **clawback-watchdog**
- Paste your key → Click **Save key**

Or via terminal:
```bash
openclaw config set skills.entries.clawback-watchdog.apiKey "your-etherscan-key"
```

#### URLScan.io → unlocks url-scan Level 3

Adds visual screenshot analysis, phishing kit detection, and DOM inspection.

1. Sign up at [urlscan.io](https://urlscan.io)
2. Go to Settings → API
3. Free tier: 100 scans/day

Setup via Control UI or:
```bash
openclaw config set skills.entries.clawback-url-scan.env.URLSCAN_API_KEY "your-urlscan-key"
```

#### Chainabuse → unlocks community scam reporting

Enables direct submission of scam addresses to the Chainabuse community database.

1. Sign up at [chainabuse.com](https://www.chainabuse.com)
2. Generate an API key

Setup via Control UI or:
```bash
openclaw config set skills.entries.clawback-report.apiKey "your-chainabuse-key"
```

### API Summary

| API | Signup | Free? | What it unlocks |
|---|---|---|---|
| GoPlus Security | None needed | ✅ No key required | Token security, approval audit, address reputation |
| VirusTotal | [virustotal.com](https://virustotal.com) | ✅ Free tier | 90+ engine scanning, domain reputation |
| Etherscan | [etherscan.io](https://etherscan.io) | ✅ Free (ETH/Polygon/Arbitrum) | Airdrop honeypot detection in watchdog |
| URLScan.io | [urlscan.io](https://urlscan.io) | ✅ Free tier | Visual phishing analysis |
| Chainabuse | [chainabuse.com](https://chainabuse.com) | ✅ Free tier | Community scam reporting |

---

## Chain Support

ClawBack auto-detects chains where possible. Token-check tries multiple chains until data is found. Watchdog asks which chain to monitor.

| Chain | Chain ID | token-check | watchdog | Notes |
|---|---|---|---|---|
| **Ethereum** | 1 | ✅ Auto-detect | ✅ Full (free Etherscan) | Recommended for full 3-check monitoring |
| **BSC / BNB Chain** | 56 | ✅ Auto-detect | ⚠️ GoPlus only | Airdrop detection needs Etherscan Pro |
| **Polygon** | 137 | ✅ Auto-detect | ✅ Full (free Etherscan) | Recommended for full 3-check monitoring |
| **Arbitrum** | 42161 | ✅ Auto-detect | ✅ Full (free Etherscan) | Recommended for full 3-check monitoring |
| **Base** | 8453 | ✅ Auto-detect | ⚠️ GoPlus only | Block explorer coverage varies |
| **Avalanche** | 43114 | ✅ Auto-detect | ⚠️ GoPlus only | Block explorer coverage varies |
| **Solana** | solana | ⚠️ Partial | ❌ Not supported | Token security only via GoPlus |

---

## OpenClaw Setup

### Prerequisites

- [OpenClaw](https://openclaw.ai) installed and running
- An LLM API key (Anthropic recommended — ClawBack is optimised for Claude)
- Python 3 (for live API calls — pre-installed on macOS/Linux)

### Install ClawBack

```bash
# Clone and install
git clone https://github.com/LeverCRO/clawback.git
cp -r clawback/clawback-* ~/.openclaw/skills/
```

**Note:** ClawHub publishing is coming soon. For now, install directly from GitHub.

### Required OpenClaw config

For ClawBack to execute live API calls and for watchdog to persist state across sessions, add to `~/.openclaw/openclaw.json`:

```json
{
  "memory": {
    "memoryFlush": false,
    "persistenceMode": "hybrid",
    "searchStrategy": "qmd"
  },
  "tools": {
    "profile": "full"
  }
}
```

- `memoryFlush: false` — watchdog wallet registrations and scan baselines survive restarts
- `tools.profile: "full"` — enables Python and shell execution for live API calls (required for token-check, url-scan, watchdog)

Without `tools.profile: "full"`, the skills still work but use LLM knowledge instead of live data. With it, they execute real WHOIS lookups, DNS queries, GoPlus API calls, and VirusTotal scans.

---

## Detection Methodology

**Social engineering detection** (clawback-analyse) applies two diagnostic layers:

1. **Cialdini's 7 Principles of Influence** — Authority, Scarcity, Social Proof, Reciprocity, Commitment, Liking, Unity. Every scam message weaponises at least one. ClawBack names which ones and explains why they work.

2. **Crypto-specific attack vectors** — 12 documented patterns including fake exchange support, pig butchering, honeypot tokens, rug pulls, wallet drainers, recovery scams, voice phishing, deepfake impersonation, and address poisoning.

**On-chain detection** (clawback-token-check) checks 15+ contract-level flags via GoPlus Security, including honeypot mechanics, hidden mint functions, ownership status, liquidity locks, buy/sell taxes, blacklist functions, proxy patterns, and holder concentration. Auto-detects which chain the token is on.

**URL intelligence** (clawback-url-scan) operates at three levels:
- **Level 1** (zero config): Known domain matching, WHOIS lookup, DNS resolution, HTTP checks, LLM heuristics
- **Level 2** (+VirusTotal key): 90+ security engine scanning, domain reputation, certificate analysis
- **Level 3** (+URLScan key): Visual screenshot analysis, phishing kit detection, DOM inspection

**Proactive monitoring** (clawback-watchdog) runs three checks via OpenClaw cron:
- **Check 1**: Approval security — flags stale approvals to compromised contracts (GoPlus, no key)
- **Check 2**: Airdrop detection — catches honeypot tokens airdropped to your wallet (Etherscan key)
- **Check 3**: Address reputation — monitors addresses you've interacted with for retroactive flags (GoPlus, no key)

---

## Tested & Verified

All skills tested live with real API calls. Not simulated. Not hallucinated. Real data.

| Test | Result |
|---|---|
| Analyse fake Binance WhatsApp message | ✅ SCAM (99%) — identified Authority + Scarcity |
| Token-check WBNB (BSC) | ✅ SAFE — live GoPlus: 0% tax, no honeypot, ownership renounced |
| Token-check CAKE (BSC) | ✅ SAFE — correctly explained blacklist as governance feature |
| Token-check USDT (Ethereum) | ✅ Auto-detected Ethereum chain, 13.1M holders, flagged mint/blacklist as expected |
| Token-check Binance-Peg ETH (BSC) | ✅ Auto-detected BSC, 2.3M holders, flagged mint as bridge feature |
| URL-scan google.com | ✅ SAFE — live VirusTotal: 66 harmless / 0 malicious, reputation 685 |
| URL-scan metamask.io | ✅ SAFE — live VirusTotal, WHOIS: 10+ years, Cloudflare CDN |
| URL-scan free-crypto-airdrop.com | ✅ PHISHING — VirusTotal showed 0 flags but ClawBack correctly overrode from pattern analysis |
| Watchdog registration | ✅ Chain selection prompt, cron job created, initial GoPlus scan completed |
| Watchdog Vitalik's wallet (Ethereum) | ✅ Found 4 honeypot airdrops: 97% sell tax, blacklists, unverified contracts |
| Watchdog forced cron run | ✅ Cron fires, all checks execute, baseline diff works |
| Train quiz | ✅ Interactive — one round at a time, waits for answer, scores correctly |
| Learn glossary | ✅ All 30 terms, 12 scam types, 4-section security guide accessible |
| Report incident | ✅ Guided interview — one step at a time, generates formal report |

---

## Repository Structure

```
clawback/
├── README.md
├── clawback-suite/
│   └── SKILL.md          # Meta-skill — single install, routes commands
├── clawback-analyse/
│   └── SKILL.md          # Social engineering detection
├── clawback-token-check/
│   └── SKILL.md          # On-chain security scanner
├── clawback-url-scan/
│   └── SKILL.md          # URL and phishing detection
├── clawback-watchdog/
│   └── SKILL.md          # Proactive wallet monitoring
├── clawback-train/
│   └── SKILL.md          # Interactive quizzes + security setup
├── clawback-learn/
│   └── SKILL.md          # Security education reference
└── clawback-report/
    └── SKILL.md          # Incident reporting + threat intelligence
```

---

## About

Built by **Vance Lever** ([@LeverCRO](https://x.com/LeverCRO)) — AI CRO, agentic economy builder, and crypto security researcher.

The detection methodology draws on years of professional experience in social engineering defence — including published research on manipulation techniques, presentations at major security conferences on voice phishing attack patterns, and hands-on experience building enterprise-grade social engineering simulation and training platforms.

ClawBack is built on the principle that the best defence against social engineering is understanding exactly how it works — naming the weapons, mapping the playbook, and breaking the emotional spell that makes intelligent people hand over their keys.

---

## Links

- **Website**: [clawback.bot](https://clawback.bot)
- **Builder**: [iamvance.ai](https://iamvance.ai)
- **X**: [@LeverCRO](https://x.com/LeverCRO)
- **GitHub**: [github.com/LeverCRO/clawback](https://github.com/LeverCRO/clawback)

---

## License

MIT
