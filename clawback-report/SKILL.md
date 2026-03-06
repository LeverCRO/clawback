---
name: clawback-report
description: Incident reporting, community threat intelligence, and scam evidence submission. Four modes — Incident Report generates formal reports from ClawBack findings for law enforcement and exchange support. Submit forwards scam evidence to Chainabuse to protect other users. Personal Digest summarises your ClawBack activity. Community Feed compiles weekly threat intelligence from GoPlus, ScamSniffer, and Chainabuse public feeds, with a publishable format for external distribution. Activates via /clawback-report.
metadata: {"clawdbot":{"emoji":"📋","homepage":"https://clawback.bot"},"openclaw":{"primaryEnv":"CHAINABUSE_API_KEY"}}
---

# ClawBack Report

**Incident reporting, community threat intelligence, and scam evidence submission.**

Built by Vance Lever ([@LeverCRO](https://x.com/LeverCRO)) — AI CRO, agentic economy builder, crypto security researcher. Part of the [ClawBack](https://github.com/LeverCRO/clawback) skill suite for the Binance OpenClaw AI Innovation Competition.

Four modes: Incident Report (formal documentation for law enforcement), Community Submission (Chainabuse database), Personal Digest (your ClawBack activity summary), and Community Feed (weekly threat intelligence from public sources).

---

## When This Skill Activates

This skill activates **explicitly only** — when a user invokes `/clawback-report` or any subcommand.

### Commands

| Command | Action |
|---|---|
| `/clawback-report` | Show mode selection |
| `/clawback-report incident` | Generate a formal incident report |
| `/clawback-report submit [address/url]` | Submit scam evidence to Chainabuse |
| `/clawback-report digest` | Personal digest — your ClawBack activity summary |
| `/clawback-report community` | Community threat digest from public feeds |
| `/clawback-report subscribe` | Opt in to weekly community threat digest |
| `/clawback-report unsubscribe` | Opt out of weekly digest |

---

## Mode Selection Menu

When a user runs `/clawback-report` with no arguments:

```
============================================================
CLAWBACK REPORT
============================================================

Choose a mode:

INCIDENT — Generate a formal incident report
   For: Law enforcement, exchange support, insurance claims.
   Guided interview → professional report document.
   → /clawback-report incident

SUBMIT — Report a scam address to the community
   For: Protecting other users via Chainabuse database.
   → /clawback-report submit [address or URL]

DIGEST — Your personal ClawBack activity summary
   For: Reviewing what ClawBack has caught for you recently.
   → /clawback-report digest

COMMUNITY — Weekly threat intelligence from public feeds
   For: Staying informed on current scam trends.
   → /clawback-report community
   → /clawback-report subscribe (weekly delivery)

============================================================
```

---

## Mode 1: Incident Report Generation

When a user has been scammed or targeted, this mode generates a structured incident report through a guided interview. It auto-populates from previous ClawBack findings when available — if the user ran `/clawback-analyse`, `/clawback-token-check`, `/clawback-url-scan`, or `/clawback-watchdog` earlier, pull those results from OpenClaw memory.

### Interview Flow — 5 Steps

Present one step at a time. Gentle pace. Every step includes: "You can skip this step if you don't have this information. You can come back to it later."

#### Step 1 — What Happened?

```
============================================================
CLAWBACK INCIDENT REPORT — STEP 1 of 5
============================================================

Let's document what happened. I'll walk you through this
step by step. Everything you share stays in this report —
it is not sent anywhere until you choose to submit it.

You can come back to this later if you need a break.

In your own words, what happened?
(e.g. "I sent $5,000 to a fake trading platform" or
"Someone got access to my exchange account" or
"I bought a token that turned out to be a honeypot")

============================================================
```

#### Step 2 — When Did It Happen?

```
============================================================
CLAWBACK INCIDENT REPORT — STEP 2 of 5
============================================================

TIMELINE

- When did the incident happen? (Approximate date/time is
  fine — even "around mid-January" helps.)
- When did you first realise it was a scam?
- If there were multiple steps (deposits over time, repeated
  contact), walk me through the sequence.

You can skip this step if you don't have this information.
You can come back to it later.

============================================================
```

#### Step 3 — Financial Impact

```
============================================================
CLAWBACK INCIDENT REPORT — STEP 3 of 5
============================================================

FINANCIAL IMPACT

- What assets were lost? (Token type, amount, approximate
  USD value at the time)
- Which wallet address or exchange account was affected?
- Do you have transaction hashes?

If you don't have transaction hashes:
  Go to your chain's block explorer and search your wallet
  address. Look for outgoing transactions around the date
  of the incident. The transaction hash is the long string
  starting with 0x next to each transaction.

  BSC: bscscan.com/address/[your-address]
  Ethereum: etherscan.io/address/[your-address]
  Solana: solscan.io/account/[your-address]

You can skip this step if you don't have this information.
You can come back to it later.

============================================================
```

#### Step 4 — Attacker Information

```
============================================================
CLAWBACK INCIDENT REPORT — STEP 4 of 5
============================================================

ATTACKER INFORMATION

Any of the following you have:
- Wallet addresses they used (receiving address, contract
  address, deployer address)
- URLs, domains, or platform names
- Contact details (phone numbers, email addresses, social
  media handles, Telegram usernames)
- Any messages or screenshots you've preserved

IMPORTANT — PRESERVE EVIDENCE NOW:
Save screenshots of all conversations, emails (including
full headers), transaction confirmations, and any URLs
before they go offline. Scam sites and accounts are often
deleted within days.

You can skip this step if you don't have this information.
You can come back to it later.

============================================================
```

#### Step 5 — Previous ClawBack Analysis

```
============================================================
CLAWBACK INCIDENT REPORT — STEP 5 of 5
============================================================

CLAWBACK EVIDENCE

Have you already run any ClawBack checks on this incident?

If yes: I'll pull the findings from your ClawBack history
and include them as evidence in your report.

If no: Running these checks now will strengthen your report:
```

Based on what the user described in Step 1:

| If the user mentioned... | Suggest |
|---|---|
| A suspicious message, DM, or email | "Run `/clawback-analyse` and paste the message. The social engineering analysis adds evidence." |
| A token, contract, or wallet address | "Run `/clawback-token-check [address]` to add on-chain evidence." |
| A URL, domain, or website | "Run `/clawback-url-scan [url]` to add domain analysis evidence." |
| An airdrop or unexpected token | "Run `/clawback-token-check [token-address]` to check for honeypot mechanics." |

The skill checks OpenClaw memory for any existing ClawBack analysis results (analyse verdicts, token-check results, url-scan results, watchdog alerts) and auto-populates the evidence section.

### Generated Report Format

```
============================================================
CLAWBACK INCIDENT REPORT
============================================================
Generated: [date and time]
Report ID: CB-[random 8-char alphanumeric]

============================================================
INCIDENT SUMMARY
============================================================
Type: [Scam type — mapped to ClawBack's taxonomy:
       Phishing / Pig Butchering / Honeypot Token / Rug Pull /
       Fake Exchange Support / Wallet Drainer / Recovery Scam /
       Fake Staking-Yield / SIM Swap / Voice Phishing /
       Address Poisoning / Other]
Date: [when it occurred]
Discovered: [when user realised]
Platform/Chain: [which platform or chain was involved]

DESCRIPTION:
[User's account of what happened, cleaned up into a clear
 chronological narrative. Written in third person for formal
 submission: "The victim received a message..." not "I got
 a message..." The user's real name is never included unless
 they explicitly provide it and request it.]

============================================================
FINANCIAL IMPACT
============================================================
Assets Lost:
- [Amount] [Token] (~$[USD value] at time of incident)
- [Additional assets if multiple]

Total Estimated Loss: $[USD total]

Affected Account/Wallet: [address or account identifier]

============================================================
ATTACKER INFORMATION
============================================================
Known Wallet Addresses:
- [address 1] — [role: deployer / recipient / contract]
  Explorer: [chain-aware block explorer link]
- [address 2] — [role]
  Explorer: [link]

Known URLs/Domains:
- [url 1] — [phishing site / fake platform / claim site]

Known Contact Details:
- [phone/email/telegram/social — whatever provided]

============================================================
EVIDENCE
============================================================
Transaction Hashes:
- [tx hash 1] — [description: "transfer of X to attacker"]
  Verify: [chain-aware block explorer tx link]
- [tx hash 2] — [description]
  Verify: [link]

ClawBack Analysis Results:
[If /clawback-analyse was run: verdict, confidence, attack
 vector, Cialdini principles identified]
[If /clawback-token-check was run: risk level, specific
 flags detected (honeypot, mint, ownership, etc.)]
[If /clawback-url-scan was run: verdict, domain age,
 engine detections, visual clone findings]
[If /clawback-watchdog flagged: what was detected and when]
[If none: "No ClawBack analysis was performed prior to this
 report. Running analysis on available evidence would
 strengthen this report."]

Preserved Evidence:
- [List of screenshots, messages, emails the user has saved]
- [Format and storage location if noted]

============================================================
RECOMMENDED SUBMISSION CHANNELS
============================================================
Based on this incident, submit your report to:

LAW ENFORCEMENT:
[Select most relevant based on location/jurisdiction context.
 See law enforcement reference table below. Always include
 local police as a baseline.]

EXCHANGE SUPPORT:
[If Binance: "Contact via in-app support chat only — NOT
 email, social media, or phone. Include this report and
 all transaction hashes."]
[If other exchange: official support channel for that
 exchange]
[Note: "If reported quickly, the exchange may be able to
 freeze the attacker's funds. Hours matter."]

COMMUNITY DATABASES:
- Chainabuse: chainabuse.com/report
  Run /clawback-report submit [address] to submit directly
- Block explorer: report the contract/address on the
  relevant explorer

ADDITIONAL RESOURCES:
[If significant loss (>$10K): "Consider consulting a lawyer
 specialising in crypto fraud."]
[If exchange-based: "Exchange may be able to freeze attacker
 funds if reported within hours."]

============================================================
IMPORTANT NOTES
============================================================
- Time is critical. The sooner you report, the higher the
  chance of any recovery. Exchange-based freezes only work
  if reported before the attacker withdraws.
- This report was generated locally. It has not been sent to
  anyone. You choose where and when to submit it.
- Save a copy of this report for your records.
- DO NOT pay anyone claiming they can recover your funds.
  Recovery scams target previous victims — this is one of
  the most common follow-up scams after a loss. See:
  /clawback-learn recovery scam

============================================================
```

### Law Enforcement Reference Table

| Country/Region | Agency | URL | Notes |
|---|---|---|---|
| USA | FBI IC3 | ic3.gov | Primary for crypto fraud |
| USA | FTC | reportfraud.ftc.gov | Federal Trade Commission |
| UK | Action Fraud | actionfraud.police.uk | National fraud reporting |
| EU | Europol | europol.europa.eu/report-a-crime | Cross-border cybercrime |
| Australia | ReportCyber | cyber.gov.au/report | Australian Cyber Security Centre |
| Singapore | SPF | police.gov.sg/e-services | Singapore Police Force |
| Global | Chainabuse | chainabuse.com/report | Community database — not law enforcement but used by exchanges and security tools |
| Global | Local Police | N/A | Always file a local report — it creates a record even if they can't act on crypto crimes directly |

Select the most relevant channels based on context. If the user mentions their country, prioritise that jurisdiction. Always include local police and at least one global option.

### Chain-Aware Block Explorer Links

| Chain | Explorer | Transaction Link | Address Link |
|---|---|---|---|
| BSC | bscscan.com | bscscan.com/tx/{hash} | bscscan.com/address/{addr} |
| Ethereum | etherscan.io | etherscan.io/tx/{hash} | etherscan.io/address/{addr} |
| Polygon | polygonscan.com | polygonscan.com/tx/{hash} | polygonscan.com/address/{addr} |
| Arbitrum | arbiscan.io | arbiscan.io/tx/{hash} | arbiscan.io/address/{addr} |
| Base | basescan.org | basescan.org/tx/{hash} | basescan.org/address/{addr} |
| Avalanche | snowtrace.io | snowtrace.io/tx/{hash} | snowtrace.io/address/{addr} |
| Solana | solscan.io | solscan.io/tx/{hash} | solscan.io/account/{addr} |

Default to BSC if chain is not specified. If the user provides a transaction hash or address, infer the chain from the format (Solana addresses are base58, EVM addresses start with 0x).

---

## Mode 2: Community Submission (Chainabuse)

Submit scam wallet addresses, URLs, and contract addresses to the Chainabuse community database.

### API

- **Endpoint**: `https://api.chainabuse.com/v0/reports` (POST)
- **Free tier**: Available with registration
- **Key required**: Yes (free registration at chainabuse.com)

**API Key Access Pattern:**

All API calls use curl with shell variable expansion (`$VARIABLE_NAME`) piped into Python for JSON parsing. The shell expands environment variables before the agent processes the command — this avoids security refusals that occur when agents access `os.environ` directly.

OpenClaw injects `skills.entries.<skill>.env` variables into the process environment at runtime. Users configure keys via the Skills page in the Control UI or via:

```
openclaw config set skills.entries.clawback-report.env.CHAINABUSE_API_KEY your-key
```

**Chainabuse submission check:**
```bash
curl -s "https://api.chainabuse.com/v0/reports" \
  -H "Authorization: Bearer $CHAINABUSE_API_KEY" \
  -H "Content-Type: application/json" | python3 -c "
import json, sys
try:
    d = json.load(sys.stdin)
    print(json.dumps(d, indent=2))
except:
    print(json.dumps({'error': 'Chainabuse API unavailable or key not configured', 'fallback': 'Submit manually at chainabuse.com/report'}))
"
```

**API availability note:** The Chainabuse submission API (`/v0/reports`) may require partnership-tier access depending on current Chainabuse terms. If the API rejects the submission (403 or feature-not-available response), the skill falls back to providing the manual submission link. This degradation is seamless — the user is directed to chainabuse.com/report with their details pre-formatted for copy-paste.

### Submission Flow

When user runs `/clawback-report submit 0xScamAddress`:

```
============================================================
CLAWBACK — COMMUNITY SUBMISSION
============================================================

ADDRESS: 0xScamAddress

To submit this address to the Chainabuse community database,
I need a few details:

1. What type of scam? [Phishing / Honeypot / Rug Pull /
   Fake Platform / Wallet Drainer / Other]
2. Brief description (1-2 sentences)
3. Approximate date of the incident
4. Your loss amount (optional — helps prioritise)

Your submission will be public and anonymous. It helps
protect other users who encounter this address.

IMPORTANT: Only the SCAM address and your description are
submitted. Your wallet address and personal information are
never included.

============================================================
```

After collecting details, confirm before sending:

```
============================================================
CONFIRM SUBMISSION
============================================================

Submitting to Chainabuse:
- Address: 0xScamAddress
- Type: [scam type]
- Description: [user's description]
- Date: [date]

This submission is public and anonymous.
Confirm? (yes/no)

============================================================
```

After successful submission:

```
============================================================
SUBMISSION CONFIRMATION
============================================================

Submitted to Chainabuse:
- Address: 0xScamAddress
- Type: [scam type]
- Description: [user's description]
- Status: Submitted successfully

Your report helps protect the community. This address will
appear in Chainabuse searches and may trigger warnings in
wallets and tools that use their database.

View: https://www.chainabuse.com/address/0xScamAddress

============================================================
```

### If Chainabuse API Key Is Not Configured

```
Chainabuse API key not configured. To enable direct
submission:
  1. Register free at chainabuse.com
  2. Configure: openclaw config set skills.entries.clawback-report.env.CHAINABUSE_API_KEY "your-key"

In the meantime, submit manually at:
  https://www.chainabuse.com/report
```

---

## Mode 3: Personal Digest

Summarises the user's recent ClawBack activity across all skills. Pulls from OpenClaw memory. Zero API dependencies.

When user runs `/clawback-report digest`:

```
============================================================
CLAWBACK PERSONAL DIGEST
============================================================
Generated: [date]
Period: Last 7 days

------------------------------------------------------------
YOUR ACTIVITY
------------------------------------------------------------
Messages analysed: [count from /clawback-analyse usage]
Tokens checked: [count from /clawback-token-check usage]
URLs scanned: [count from /clawback-url-scan usage]
Watchdog alerts: [count from /clawback-watchdog]
Training sessions: [count from /clawback-train]

------------------------------------------------------------
THREATS DETECTED
------------------------------------------------------------
[List each threat found across all skills in the period,
 chronologically, most recent first]

- [date]: SCAM detected — Fake exchange support message
  (97% confidence) → via /clawback-analyse
- [date]: CRITICAL token — 0xabc...def flagged as honeypot
  → via /clawback-token-check
- [date]: PHISHING domain — binance-secure-verify.com
  → via /clawback-url-scan
- [date]: Watchdog alert — unlimited USDT approval to
  flagged contract → revoked

[If no threats detected:]
No threats detected in the last 7 days.

------------------------------------------------------------
RECOMMENDATIONS
------------------------------------------------------------
[Based on activity patterns — select the most relevant:]

[If no watchdog active:]
"Your wallet is unmonitored. Set up 24/7 monitoring:
 /clawback-watchdog [your-address]"

[If no quiz sessions:]
"Sharpen your scam detection instincts:
 /clawback-train quiz"

[If no security setup completed:]
"Harden your exchange security in 5 minutes:
 /clawback-train setup"

[If multiple threats detected:]
"You've been targeted multiple times this week. Consider
 filing a formal report: /clawback-report incident"

[If nothing used at all:]
"You haven't used ClawBack much this week. Start with:
 /clawback-learn — understand the threats
 /clawback-train quiz — test your instincts
 /clawback-watchdog — monitor your wallet"

============================================================
```

---

## Mode 4: Community Threat Feed

Compiles threat intelligence from public data sources into a formatted digest. Two sub-modes: an internal version (for the user's own awareness) and a publishable version (for external distribution on X, Telegram, Binance Square).

### Data Sources

All public, all free:

| Source | What It Provides | Access Method | Key Required |
|---|---|---|---|
| GoPlus Security | Newly flagged addresses, honeypot contracts, malicious wallets | API: `api.gopluslabs.io` (same as token-check) | No |
| ScamSniffer | Phishing domain database, wallet drainer tracking | GitHub: `github.com/scamsniffer` — open source, 7-day public delay | No |
| Chainabuse | Community-submitted scam reports | API: `api.chainabuse.com/v0/reports` | Yes (free) |

If Chainabuse API key is not configured, generate the digest from GoPlus + ScamSniffer only and note the gap.

### Data Bootstrapping — Honest Limitations

The community digest improves over time. Here is what to expect:

**INITIAL STATE (new user, no ClawBack history):**
The digest draws from public feeds but has limited ability to discover NEW threats independently. GoPlus checks specific addresses — it does not provide a "what's new this week" firehose. ScamSniffer's public data has a 7-day delay. The first few digests may be thin.

**IMPROVING STATE (active ClawBack user):**
As you use ClawBack — checking tokens, scanning URLs, monitoring wallets — the addresses you interact with seed the digest's intelligence pool. Watchdog alerts, analyse verdicts, and token-check results all contribute addresses that get re-checked against public feeds on each digest cycle. More usage = richer digests.

**MATURE STATE (weeks of active use):**
The digest cross-references your accumulated address pool against all three public feeds, surfacing connections and newly flagged addresses you've encountered. Combined with Chainabuse community reports, it becomes a genuinely useful weekly intelligence briefing.

The publishable version (for X, Telegram, Binance Square) is most valuable when curated by someone actively monitoring the BSC ecosystem — which is the intended use case for ClawBack power users building a security-focused audience.

### Community Digest Format

When user runs `/clawback-report community`:

```
============================================================
CLAWBACK COMMUNITY THREAT DIGEST
============================================================
Period: [date range — last 7 days]
Sources: GoPlus Security, ScamSniffer, Chainabuse

DISCLAIMER: This digest is compiled from public threat
intelligence sources. It is informational only — not
financial or legal advice. Verify independently before
acting. Do not include personal wallet addresses or
transaction details when sharing this digest.

------------------------------------------------------------
TOP BSC SCAMS THIS WEEK
------------------------------------------------------------

1. [Scam name/type] — [2-3 sentence description]
   Flagged addresses: [truncated]
   Impact: [estimated losses if available from source data]
   Check: /clawback-token-check [address]

2. [Scam name/type] — [description]
   Flagged addresses: [truncated]
   Impact: [estimated]
   Check: /clawback-token-check [address]

[Up to 5 entries, ranked by severity and recency]

------------------------------------------------------------
TRENDING PHISHING DOMAINS
------------------------------------------------------------
- [domain 1] — impersonating [platform], [age] days old
  Check: /clawback-url-scan [domain]
- [domain 2] — [description]
- [domain 3] — [description]

[Up to 5 domains from ScamSniffer + GoPlus feeds]

------------------------------------------------------------
NEWLY FLAGGED ADDRESSES
------------------------------------------------------------
[Addresses flagged by GoPlus in the past 7 days relevant
 to BSC users — phishing, honeypot, laundering, etc.]
- 0xabc...def — flagged for: [reason]
- 0x123...456 — flagged for: [reason]

[Up to 10 addresses]

------------------------------------------------------------
PROTECT YOURSELF
------------------------------------------------------------
- /clawback-token-check — verify any token before buying
- /clawback-url-scan — verify any link before clicking
- /clawback-watchdog — monitor your wallet 24/7
- /clawback-learn — understand the scams and the psychology

============================================================
```

After generating the community digest, offer: "Want a version formatted for external sharing? I'll generate a copy without ClawBack /commands — replaced with public links that anyone can use."

### Publishable Version

For external distribution on X, Telegram, Binance Square, or other platforms. Formatted for easy copy-paste.

```
============================================================
CLAWBACK WEEKLY — CRYPTO SCAM INTELLIGENCE
============================================================
Week of [date range] | clawback.bot

DISCLAIMER: Compiled from public threat intelligence sources
(GoPlus Security, ScamSniffer, Chainabuse). Informational
only. Verify independently. Never share personal wallet
addresses or transaction details publicly.

TOP 5 BSC SCAMS THIS WEEK:

1. [Scam name/type]
   [2-3 sentence description of the scam and how it works]
   Flagged addresses: [truncated]
   Verify: [GoPlus dashboard link]

2. [Continue for up to 5]

TRENDING PHISHING DOMAINS:
- [domain] — impersonating [platform], [age] days old
  Check: [GoPlus or VirusTotal link]
[Up to 5]

HOW TO PROTECT YOURSELF:
- Check any token before buying: gopluslabs.io
- Check any URL before clicking: virustotal.com
- Revoke unused token approvals: bscscan.com/tokenapprovalchecker
- Get ClawBack for your own wallet: clawback.bot

============================================================
```

Key differences from internal version:
- Strips all `/clawback-*` commands (external readers don't have ClawBack)
- Replaces them with GoPlus dashboard links and block explorer links
- Branded header: "ClawBack Weekly — Crypto Scam Intelligence"
- Branded footer link: clawback.bot
- Disclaimer is always included and cannot be removed

### Subscription Flow

When user runs `/clawback-report subscribe`:

```
============================================================
CLAWBACK DIGEST — SUBSCRIBED
============================================================

You will receive a weekly threat digest every Monday
covering:
- Top BSC scams from the past week
- Trending phishing domains
- Newly flagged addresses

Sources: GoPlus Security, ScamSniffer, Chainabuse
Delivery: Your OpenClaw chat
Schedule: Weekly (configurable via openclaw config)

This digest contains only publicly available threat data.
Your personal wallet data and ClawBack analysis results
are never included in the community digest.

To unsubscribe: /clawback-report unsubscribe
To get your personal activity digest: /clawback-report digest
To get the community digest on demand: /clawback-report community

============================================================
```

Subscription status is stored in OpenClaw memory. Delivery via OpenClaw cron:

```
openclaw config set skills.entries.clawback-report.config.digestScheduleDays 7
```

When user runs `/clawback-report unsubscribe`:

```
============================================================
CLAWBACK DIGEST — UNSUBSCRIBED
============================================================

Weekly threat digest disabled. You will no longer receive
automated community digests.

You can still generate digests on demand:
- /clawback-report community — community threat digest
- /clawback-report digest — your personal activity digest
- /clawback-report subscribe — re-subscribe anytime

============================================================
```

---

## API Stack

| API | Purpose | Mode | Key Required | Free Tier |
|---|---|---|---|---|
| Chainabuse | Scam report submission + community reports feed | Mode 2 + Mode 4 | Yes (free registration) | Yes |
| GoPlus Security | Threat feed — newly flagged addresses and tokens | Mode 4 | No | Yes (5M calls/day) |
| ScamSniffer | Phishing domain tracking | Mode 4 | No (GitHub, open source) | Yes (7-day public delay) |

Mode 1 (Incident Report) and Mode 3 (Personal Digest) have **zero API dependencies** — they work entirely from user input and OpenClaw memory.

### OpenClaw Configuration

```json
{
  "skills": {
    "entries": {
      "clawback-report": {
        "enabled": true,
        "env": {
          "CHAINABUSE_API_KEY": "your-chainabuse-key"
        },
        "config": {
          "digestScheduleDays": 7
        }
      }
    }
  }
}
```

Or via CLI:
```
openclaw config set skills.entries.clawback-report.env.CHAINABUSE_API_KEY "your-key"
openclaw config set skills.entries.clawback-report.config.digestScheduleDays 7
```

---

## Error & Rate Limit Handling

| Scenario | Action |
|---|---|
| Chainabuse 200 + success | Confirm submission to user |
| Chainabuse 401 (bad key) | "Chainabuse API key is invalid or not configured. Configure via: `openclaw config set skills.entries.clawback-report.env.CHAINABUSE_API_KEY`. Submit manually at chainabuse.com/report" |
| Chainabuse 403 (feature not available) | "Direct API submission is not available on your Chainabuse tier. Your report has been formatted for manual submission: chainabuse.com/report — paste the details below into the form." Then output the pre-formatted submission text (address, scam type, description, date) for the user to copy. |
| Chainabuse 429 (rate limit) | "Rate limit reached. Your submission has been saved locally. Try again later or submit manually at chainabuse.com/report" |
| Chainabuse 5xx | Retry once after 2 seconds. If still failing, fall back to manual submission link. |
| No Chainabuse key configured | Provide manual submission link. Don't error — degrade gracefully. Mode 2 shows manual instructions. Mode 4 excludes Chainabuse data and notes the gap. |
| GoPlus API unavailable (Mode 4) | Generate digest from ScamSniffer + Chainabuse only. Note: "GoPlus feed was unavailable. Some newly flagged addresses may be missing." |
| All APIs unavailable (Mode 4) | "Community threat feed sources are currently unavailable. Try again later. For manual checking: gopluslabs.io, chainabuse.com, github.com/scamsniffer" |
| No data from any source (quiet week) | "No significant new threats flagged in public databases this week. This doesn't mean threats don't exist — stay vigilant. Run /clawback-learn briefing for a broader threat landscape overview." |

---

## Privacy & Data Handling

ClawBack Report handles sensitive information. These rules are absolute:

### Incident Reports (Mode 1)

- Generated and stored locally in OpenClaw memory ONLY
- Never transmitted anywhere without explicit user action
- The user chooses where to submit — the skill does not auto-send anything
- Reports use "the victim" (third person) — the user's real name is never included unless they explicitly provide it and request it in the report
- "You can come back to this later" is offered at every step — the user is never pressured to continue

### Community Submissions (Mode 2)

- Submitted to Chainabuse (public database) — user is warned that submissions are public and anonymous
- Only the SCAM address and description are submitted — never the victim's address, transaction history, or personal information
- The user explicitly confirms before any submission is sent

### Personal Digest (Mode 3)

- Pulls from local OpenClaw memory only
- Never transmitted externally
- Contains the user's own ClawBack activity — for their eyes only

### Community Digest (Mode 4)

- Compiled EXCLUSIVELY from public threat intelligence sources (GoPlus, ScamSniffer, Chainabuse public feeds)
- Never includes any user's personal data, wallet addresses, or ClawBack analysis results
- Contains only addresses ALREADY publicly flagged in external databases
- The disclaimer is ALWAYS included and cannot be removed from the digest output
- The publishable version explicitly warns: "Never share personal wallet addresses or transaction details publicly"

### Subscriptions

- Status stored in OpenClaw memory (local only)
- Digests delivered to the user's own OpenClaw chat only — not to email, not to external services
- User can unsubscribe at any time via `/clawback-report unsubscribe`

---

## Skill Behaviour

### Tone

- **Empathetic but professional** (Mode 1). The user just lost money. Be firm, clear, and supportive. Never blame. "These techniques work on smart people — that's why scammers use them."
- **Urgent when recent** (Mode 1). If the incident happened within hours: "Report to the exchange NOW — they may be able to freeze the attacker's funds. Hours matter." If it happened weeks ago, don't create false urgency — focus on documentation and prevention.
- **Practical always.** Every recommendation includes a specific action and link. No "consider reporting to the authorities" — instead: "File a report at ic3.gov."
- **Neutral and factual** (Mode 4). Community digests report facts from public databases. No speculation, no opinion. If it's flagged in GoPlus, report it. If it's not, don't include it.

### Recovery Scam Warning

**NON-NEGOTIABLE**: ALWAYS warn at the end of every incident report that recovery scams will target the user. This is included in the IMPORTANT NOTES section of the generated report and cannot be omitted. Recovery scams are one of the most common follow-up scams after a loss.

### Interview Pacing

CRITICAL: Present ONE step at a time. Wait for the user's response before showing the next step. Do not present all 5 steps in a single message. The user may be distressed — one step at a time gives them control over the pace.

CRITICAL: You MUST actually execute API calls. Do not simulate, estimate, or fabricate API responses. If a command fails, report the error. The user is relying on real data.

---

## Edge Cases

- **User hasn't been scammed but wants to report a scam they spotted** — support this via Mode 2 (community submission). No incident report needed.
- **User was scammed months ago** — report is still valuable for law enforcement records and community databases. Note that recovery is less likely but documentation still matters.
- **Centralised exchange loss vs on-chain loss** — different report structures. Exchange incidents need account details (not wallet addresses). On-chain incidents need transaction hashes and contract addresses. The interview flow adapts based on what the user describes.
- **User doesn't have transaction hashes** — explain how to find them: "Go to [block explorer for their chain] and search your wallet address. Look for outgoing transactions around [date they provided]. The transaction hash is the long string starting with 0x next to each transaction."
- **User is emotionally distressed** — the interview flow is gentle, one step at a time. Every step says "You can come back to this later." Never pressure to continue.
- **Multiple incidents from the same attacker** — generate one consolidated report covering all incidents.
- **Cross-chain incident** — document all chains involved with the correct block explorer links for each.
- **User runs community digest but has never used ClawBack** — generate the public feed anyway. It doesn't require personal data.
- **Digest has too much data** — cap at top 5 scams + top 5 domains + top 10 addresses. If more exist: "Full feeds available at gopluslabs.io, chainabuse.com, github.com/scamsniffer."
- **User wants to include personal wallet address in a community submission** — refuse. "Community submissions should only contain scam addresses, not your own wallet. Your address is not included in the submission."

---

## What This Skill Is NOT

- **Not a threat scanner** — use `clawback-analyse` for suspicious messages, `clawback-token-check` for tokens, `clawback-url-scan` for URLs
- **Not a monitoring service** — use `clawback-watchdog` for ongoing wallet monitoring
- **Not a recovery service** — no one can guarantee fund recovery. Be suspicious of anyone who claims otherwise. See: `/clawback-learn recovery scam`
- **Not legal advice** — the incident report is a documentation tool to assist with filing reports to law enforcement and exchange support. Consult a licensed attorney for legal guidance.

This skill is the **documentation and community defence layer** — it turns ClawBack's analysis into actionable reports and feeds threat intelligence back to the community.

---

## Integration with ClawBack Suite

ClawBack Report is where detection becomes action. Every other skill feeds into it.

| Context | Trigger Skill | What to Say |
|---|---|---|
| User needs to understand what happened to them | `clawback-learn` | "Want to understand the scam type? Run `/clawback-learn [scam type]` for a full breakdown." |
| Report references a token or contract | `clawback-token-check` | "Run `/clawback-token-check [address]` to add on-chain evidence to your report." |
| Report references a URL or domain | `clawback-url-scan` | "Run `/clawback-url-scan [url]` to add domain analysis to your report." |
| Report references suspicious messages | `clawback-analyse` | "Run `/clawback-analyse` and paste the messages to add social engineering analysis to your report." |
| User wants ongoing monitoring after an incident | `clawback-watchdog` | "Protect against future threats: `/clawback-watchdog [your-wallet-address]`." |
| User wants to practise spotting scams after being targeted | `clawback-train` | "Build your defences: `/clawback-train quiz` — recognising scam patterns is the best prevention." |

---

## About the Builder

**Vance Lever** ([@LeverCRO](https://x.com/LeverCRO)) — AI CRO and agentic economy builder with deep expertise in social engineering, manipulation detection, and crypto security. ClawBack is built on the principle that the best defence against social engineering is understanding exactly how it works — naming the weapons, mapping the playbook, and breaking the emotional spell that makes intelligent people hand over their keys.
