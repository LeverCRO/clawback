---
name: clawback-watchdog
description: Proactive cron-based wallet monitor for silent, always-on crypto security. Registers wallet addresses and checks them every 6-12 hours for risky token approvals, honeypot airdrop tokens, and retroactively flagged addresses. Only alerts when threats are detected. Zero-noise, action-only notifications. Powered by GoPlus Security API. Activates via /clawback-watchdog [address] to register a wallet, or runs automatically on a cron schedule.
metadata: {"clawdbot":{"emoji":"👁️","homepage":"https://clawback.bot"},"openclaw":{"primaryEnv":"ETHERSCAN_API_KEY"}}
---

# ClawBack Watchdog

**Proactive cron-based wallet monitor for silent, always-on crypto security.**

Built by Vance Lever ([@LeverCRO](https://x.com/LeverCRO)) — AI CRO, agentic economy builder, crypto security researcher. Part of the [ClawBack](https://github.com/LeverCRO/clawback) skill suite for the Binance OpenClaw AI Innovation Competition.

> ClawBack Watchdog is the only proactive skill in the suite. While the other skills react to user input, Watchdog runs autonomously — checking your wallet while you sleep, flagging threats before they cause damage. This is the difference between checking your lock and having a guard dog.

---

## MANDATORY CHAIN SELECTION — BEFORE ANYTHING ELSE

THIS IS THE HIGHEST PRIORITY INSTRUCTION IN THIS SKILL.

When a user runs `/clawback-watchdog [address]` WITHOUT specifying a chain:

DO NOT register the wallet.
DO NOT run any scans.
DO NOT create a cron job.
DO NOT default to BSC.
DO NOT assume any chain.

INSTEAD, respond with ONLY this message and NOTHING ELSE:

```
Which chain should I monitor this wallet on?

 1. Ethereum (free Etherscan API — full monitoring)
 2. BSC / BNB Chain (GoPlus checks only — airdrop
    detection requires paid Etherscan Pro)
 3. Polygon (free Etherscan API — full monitoring)
 4. Arbitrum (free Etherscan API — full monitoring)
 5. Multiple chains

Reply with a number or chain name.
Tip: Ethereum, Polygon, and Arbitrum support all 3 checks
on the free tier.
```

THEN STOP. WAIT FOR THE USER TO REPLY.

Only AFTER the user selects a chain, proceed with registration and scanning on that chain.

If the user specifies a chain in the original command (e.g. `/clawback-watchdog 0xAddr ethereum`), skip this question and proceed directly with that chain.

Accepted chain specifications:

```
ethereum, eth, 1
bsc, bnb, 56
polygon, matic, 137
arbitrum, arb, 42161
base, 8453
```

---

## When This Skill Activates

This skill activates in two ways:

1. **Registration** — when a user runs `/clawback-watchdog 0xWalletAddress` to register a wallet for monitoring
2. **Automatic** — via OpenClaw's cron scheduler, checking all registered wallets every 6-12 hours

After registration, the skill is **silent by default**. It only produces output when a threat is detected. No news = good news.

---

## Setup

This skill uses the same [GoPlus Security API](https://gopluslabs.io) as `clawback-token-check`. **No API key required** for Check 1 (approvals) and Check 3 (reputation). If GoPlus introduces key-based access in the future, configure it in `~/.openclaw/openclaw.json` under `skills.entries.clawback-watchdog.env.GOPLUS_API_KEY`.

**Optional: Block explorer API key for airdrop detection (Check 2).** GoPlus analyses tokens you submit but cannot list what a wallet holds. To detect new airdropped tokens, the skill needs a block explorer API to enumerate wallet token holdings.

**Block Explorer API — Current Status:**

BSC: BscScan has migrated to Etherscan API V2. Free tier for BSC (chain 56) is NOT available — it requires an Etherscan Pro subscription. This means Check 2 (airdrop detection) requires a paid Etherscan key for BSC wallets.

Ethereum: Free tier IS available via etherscan.io. Use your Etherscan API key with chain ID 1 for Ethereum wallet monitoring.

Other free chains: Polygon (137), Arbitrum (42161), Sepolia testnet, and others have free API access via Etherscan V2.

Without a block explorer key, Watchdog operates with GoPlus only — approval security (Check 1) and address reputation (Check 3) remain fully functional. Airdrop detection (Check 2) is unavailable.

Configure: Add your Etherscan key via the Skills page in the Control UI, or:

```
openclaw config set skills.entries.clawback-watchdog.env.ETHERSCAN_API_KEY your-key
```

This makes monitoring a two-level feature:
- **Level 1 (zero config)**: Check 1 (approval security via GoPlus) + Check 3 (address reputation via GoPlus). Two of three checks work with no API keys at all. GoPlus is free and keyless.
- **Level 2 (Etherscan key)**: All three checks — adds Check 2 (airdrop detection via block explorer token history). The Etherscan key enables the skill to enumerate wallet token holdings and detect new airdropped honeypots.

### Cron Configuration

Wallet registration and monitoring configuration live in `~/.openclaw/openclaw.json`:

```json
{
  "skills": {
    "entries": {
      "clawback-watchdog": {
        "enabled": true,
        "config": {
          "checkIntervalHours": 8,
          "maxWallets": 5
        }
      }
    }
  }
}
```

Or via CLI:

```
openclaw config set skills.entries.clawback-watchdog.config.checkIntervalHours 8
openclaw config set skills.entries.clawback-watchdog.config.maxWallets 5
```

### OpenClaw Cron Scheduling

ClawBack Watchdog uses OpenClaw's built-in cron system. When a user registers a wallet, the skill creates a recurring cron job that runs in an isolated session every 8 hours.

**Registration creates this cron job automatically:**

```
openclaw cron add \
  --name "ClawBack Watchdog" \
  --every 28800000 \
  --session isolated \
  --message "Run /clawback-watchdog check — check all registered wallets for new threats. Access wallet list and previous state from long-term memory. Execute GoPlus API calls via Python. Only produce output if threats are detected." \
  --announce \
  --channel [user's current channel] \
  --to [user's current chat]
```

Key configuration:
- `--session isolated`: Each check runs in a clean sandboxed session with access to long-term memory but no conversation history pollution.
- `--announce`: Alerts are delivered to the user's chat channel only when threats are detected. If the agent produces no output (no threats found), nothing is delivered.
- Jobs persist in `~/.openclaw/cron/jobs.json` and survive restarts.

**Manual controls:**
- Force immediate check: `openclaw cron run <jobId>`
- View run history: `openclaw cron runs --id <jobId>`
- Pause monitoring: `openclaw cron disable <jobId>`
- Resume monitoring: `openclaw cron enable <jobId>`
- Remove monitoring: `openclaw cron rm <jobId>`

Users can also configure the interval via openclaw.json:

```
openclaw config set skills.entries.clawback-watchdog.config.checkIntervalHours 8
```

---

## Commands

| Command | Action |
|---|---|
| `/clawback-watchdog 0xAddress` | Register wallet for monitoring (asks which chain) |
| `/clawback-watchdog 0xAddress ethereum` | Register wallet on a specific chain |
| `/clawback-watchdog status` | Show all monitored wallets and last check time |
| `/clawback-watchdog list` | List registered wallets |
| `/clawback-watchdog remove 0xAddress` | Stop monitoring a wallet |
| `/clawback-watchdog check` | Force an immediate check cycle (don't wait for cron) |

---

## API: GoPlus Security

Same API as `clawback-token-check` — no API key needed. Refer to `clawback-token-check` for full field mapping documentation.

**API Key Access Pattern:**

All API calls use curl with shell variable expansion (`$VARIABLE_NAME`) piped into Python for JSON parsing. The shell expands environment variables before the agent processes the command — this avoids security refusals that occur when agents access `os.environ` directly.

OpenClaw injects `skills.entries.<skill>.env` variables into the process environment at runtime. Users configure keys via the Skills page in the Control UI or via:

```
openclaw config set skills.entries.clawback-watchdog.env.ETHERSCAN_API_KEY your-key
```

If a key is not configured, the curl call will fail with a 401/403 error which the Python parser catches and reports with setup instructions.

**GoPlus token security (no key needed):**
```bash
# Replace CHAIN_ID with the registered wallet's chain ID (56=BSC, 1=ETH, etc.)
curl -s "https://api.gopluslabs.io/api/v1/token_security/CHAIN_ID?contract_addresses=ADDRESS" | python3 -c "
import json, sys
d = json.load(sys.stdin)
addr = list(d.get('result',{}).keys())[0] if d.get('result') else None
if not addr:
    print(json.dumps({'error': 'No data found'}))
    sys.exit(0)
t = d['result'][addr]
fields = ['token_name','token_symbol','is_honeypot','is_mintable','owner_address',
    'can_take_back_ownership','is_open_source','is_proxy','buy_tax','sell_tax',
    'cannot_sell_all','is_blacklisted','transfer_pausable','holder_count',
    'creator_address','creator_balance','creator_percent']
out = {f: t.get(f) for f in fields}
out['holders'] = t.get('holders', [])[:20]
out['lp_holders'] = t.get('lp_holders', [])[:10]
print(json.dumps(out, indent=2))
"
```

**Etherscan V2 API (requires key — paid for BSC, free for Ethereum/Polygon/Arbitrum):**
```bash
# Etherscan V2 — unified API for all EVM chains. Set chainid for target chain.
# BSC=56 (paid), Ethereum=1 (free), Polygon=137 (free), Arbitrum=42161 (free)
# Replace CHAIN_ID with the registered wallet's chain ID
curl -s "https://api.etherscan.io/v2/api?chainid=CHAIN_ID&module=account&action=tokentx&address=WALLET&startblock=0&endblock=99999999&sort=desc&apikey=$ETHERSCAN_API_KEY" | python3 -c "
import json, sys
d = json.load(sys.stdin)
print(json.dumps(d.get('result',[])[:20], indent=2))
"
```

See `clawback-token-check` for the full curl+Python call templates and field mapping documentation.

### Endpoints Used

| Endpoint | Purpose | Watchdog Check |
|---|---|---|
| `https://api.gopluslabs.io/api/v1/approval_security/{chain_id}?contract_addresses={wallet}` | Token approval audit | Check 1 |
| `https://api.gopluslabs.io/api/v1/token_security/{chain_id}?contract_addresses={token}` | Airdrop honeypot detection | Check 2 |
| `https://api.gopluslabs.io/api/v1/address_security/{address}` | Address reputation monitoring | Check 3 |

### Multi-Chain Support

| Chain | Chain ID | Notes |
|---|---|---|
| **BSC / BNB Chain** | `56` | Highest scam token volume. Airdrop detection requires paid Etherscan Pro. |
| Ethereum | `1` | |
| Polygon | `137` | |
| Arbitrum | `42161` | |
| Base | `8453` | |
| Avalanche | `43114` | |
| Solana | `solana` | **Partial** — Token security only. Approval scanning and address reputation are EVM-only. |

### Block Explorer Links (Chain-Aware)

When generating alert links, use the correct explorer for the wallet's chain:

| Chain | Explorer | Approval Checker |
|---|---|---|
| BSC | bscscan.com | bscscan.com/tokenapprovalchecker |
| Ethereum | etherscan.io | etherscan.io/tokenapprovalchecker |
| Polygon | polygonscan.com | polygonscan.com/tokenapprovalchecker |
| Arbitrum | arbiscan.io | arbiscan.io/tokenapprovalchecker |
| Base | basescan.org | basescan.org/tokenapprovalchecker |
| Avalanche | snowtrace.io | snowtrace.io/tokenapprovalchecker |
| Solana | solscan.io | N/A (no EVM approvals) |

---

## Monitoring Checks

Three checks run per registered wallet, per cycle. Each check compares current state against stored state from the previous cycle to detect changes.

### Check 1: Token Approval Audit

**What it finds**: Unlimited approvals, approvals to known-malicious contracts, stale approvals to contracts the user probably forgot about.

**API dependency: GoPlus only — NO API key needed.**

Query GoPlus Approval Security directly with the wallet address:
  `https://api.gopluslabs.io/api/v1/approval_security/{chain_id}?contract_addresses={address}`
  This returns whether the address has risky approvals — trust_list status, doubt_list status, malicious_behavior flags, contract_scan results, and risky_approval scoring. See `clawback-token-check` for the full verified field mapping.

This check works at Level 1 (zero config). No block explorer key required.

**Process:**

1. Query GoPlus Approval Security endpoint with the wallet address.
2. Compare against stored risk status from last cycle.
3. Flag:
   - **Contracts now on GoPlus doubt_list or with malicious_behavior flags** (a contract that was clean when the user approved it has since been flagged)
   - **Contracts with contract_scan.approval_abuse or privilege_withdraw** (dangerous contract capabilities)
   - **Contracts not on trust_list with risky_approval flags**

**Alert trigger**: Any of the above conditions detected.

**State stored**: Set of `{contract_address, last_known_risk_status}` per wallet.

### Check 2: Airdrop Honeypot Detection

**What it finds**: Tokens that appeared in the user's wallet without the user purchasing them — classic airdrop scam pattern. These tokens are designed to lure the user into interacting with a malicious contract.

**Token Balance Data Source:**

GoPlus does not provide a wallet token listing endpoint — it analyses tokens you submit. To detect new airdropped tokens, the skill needs a separate source for the wallet's current token holdings.

Recommended: Etherscan V2 API — unified endpoint for all EVM chains:

| Chain | Chain ID | Etherscan V2 Free Tier |
|---|---|---|
| BSC | 56 | **No** — requires Etherscan Pro subscription |
| Ethereum | 1 | **Yes** — free tier available |
| Polygon | 137 | **Yes** — free tier available |
| Arbitrum | 42161 | **Yes** — free tier available |
| Base | 8453 | **Yes** — free tier available |
| Avalanche | 43114 | Check etherscan.io for current status |

All chains use the same endpoint: `api.etherscan.io/v2/api?chainid={chain_id}&module=account&action=tokentx`

Configure in OpenClaw:

```
openclaw config set skills.entries.clawback-watchdog.env.ETHERSCAN_API_KEY "your-key"
```

If no Etherscan key is configured, this check is skipped and the registration confirmation will note: "Airdrop detection unavailable — Etherscan API key not configured. Approval auditing and address reputation monitoring are fully operational (GoPlus, no key needed)."

**Process (when token balance API is available):**

1. Query the wallet's current token holdings via block explorer API.
2. Compare against stored token list from last cycle.
3. For each **new token** (present now, absent last cycle):
   - Call `token_security/{chain_id}?contract_addresses={token_address}` — use the same `jq`-filtered curl pattern from clawback-token-check to extract only security-relevant fields and avoid context overflow
   - Check for: `is_honeypot`, `is_mintable`, `honeypot_with_same_creator`, `sell_tax > 0.50`, `cannot_sell_all`
4. If any new token has CRITICAL or HIGH flags, generate an alert.

**Alert trigger**: New token with honeypot, hidden mint, extreme sell tax, or other CRITICAL/HIGH flag.

**Important nuance**: Not every new token is malicious. Legitimate airdrops exist. The skill only alerts on tokens that GoPlus flags — a new token with no flags gets recorded in state but does not trigger an alert.

**State stored**: Set of `{token_address, token_name, first_seen_timestamp}` per wallet.

### Check 3: Address Reputation Monitoring

**What it finds**: Addresses the user's wallet has interacted with that have been **newly flagged** as malicious since the last check. This catches the scenario where a user interacted with a clean contract that later turned out to be a scam.

**Endpoint**: `address_security/{address}`

**API dependency: GoPlus only — NO API key needed.**

**Process:**

1. Query GoPlus Address Security endpoint directly with the wallet address and any addresses from stored interaction history.

   **Data enrichment note:** If an Etherscan key is configured, the skill can also enumerate wallet interactions via `action=txlist` to discover additional addresses to monitor. Without the key, this check monitors the wallet address itself plus any addresses accumulated in stored state from previous cycles (e.g. addresses discovered during Check 1 or Check 2 results).
2. For each tracked address, query the GoPlus address reputation endpoint.
3. Compare against last-known reputation status.
4. If an address was previously clean (no flags) and now has flags, generate an alert.

**Alert trigger**: Any previously-clean address newly flagged for phishing, cybercrime, money laundering, honeypot creation, or financial crime.

**Key flags** (from GoPlus — see `clawback-token-check` for full mapping): `phishing_activities`, `cybercrime`, `money_laundering`, `honeypot_related_address`, `financial_crime`, `blackmail_activities`, `darkweb_transactions`.

**State stored**: Set of `{address, last_known_flags, last_interaction_timestamp}` per wallet.

**Complexity note**: This is the most complex check because it requires maintaining interaction history. The skill stores a rolling list of addresses the wallet has interacted with, capped at the most recent 100 unique addresses to keep state manageable. On each cycle, it re-checks the reputation of these addresses and diffs against stored flags.

---

## State Management

The skill stores state in OpenClaw's long-term memory system, which persists across sessions and survives restarts.

**IMPORTANT — Memory Persistence Prerequisite:**

OpenClaw ships with memoryFlush enabled by default, which clears working memory on restart. For Watchdog to function correctly, the user MUST configure persistent memory:

```
Add to ~/.openclaw/openclaw.json:
{
  "memory": {
    "memoryFlush": false,
    "persistenceMode": "hybrid",
    "searchStrategy": "qmd"
  }
}
```

Without this configuration, Watchdog cannot persist wallet registrations or state between cron cycles. The registration flow should check for this and warn the user if memoryFlush is not disabled.

**How state is stored:**

OpenClaw stores long-term memory as local files in `~/.openclaw/agents/[agent]/memory/`. The skill uses the agent's memory tools to store and retrieve:

| State | Stored As |
|---|---|
| Registered wallets + chain IDs | Memory entry: "ClawBack Watchdog: monitoring wallet 0x1234...5678 on BSC (56)" |
| Last known token holdings per wallet | Memory entry: "ClawBack state [wallet]: tokens [list]" |
| Last known approval set per wallet | Memory entry: "ClawBack state [wallet]: approvals [list]" |
| Interacted addresses + reputation | Memory entry: "ClawBack state [wallet]: addresses [list with flags]" |
| Last successful check timestamp | Memory entry: "ClawBack state [wallet]: last check [timestamp]" |

On each cron cycle, the agent:
1. Retrieves all "ClawBack Watchdog" memory entries
2. Identifies registered wallets
3. For each wallet, retrieves the previous state entries
4. Runs the API checks (via Python)
5. Diffs current results against stored state
6. If changes detected: generates alert AND updates state
7. If no changes: updates timestamp only, produces no output

Long-term memory persists across isolated cron sessions. The cron session starts fresh (no conversation history) but has full access to stored memory entries from previous runs.

**State size management:** Capped at 100 tracked addresses per wallet and 200 tracked tokens per wallet. When storing state updates, the skill overwrites the previous state entry rather than appending, keeping memory bounded.

---

## Alert Format

Alerts are short and actionable. They are not full reports — the user is being interrupted, so every word must earn its place.

```
============================================================
CLAWBACK WATCHDOG ALERT
============================================================

WALLET: [truncated address: 0x1234...5678]
CHAIN: [chain name + ID]
THREAT: [one-line description of what was found]

------------------------------------------------------------
DETAILS
------------------------------------------------------------
[2-4 lines explaining the specific finding in plain English]

------------------------------------------------------------
ACTION REQUIRED
------------------------------------------------------------
- [ONE primary action, with link]
- [Optional secondary action]
- [Run /clawback-token-check or /clawback-analyse for full analysis]

============================================================
```

### Alert Rules

| Rule | Detail |
|---|---|
| **Lead with the threat** | The THREAT line must be understandable in isolation. If the user reads nothing else, they know what's wrong. |
| **One primary action** | The first bullet in ACTION REQUIRED is the single most important thing to do. Everything else is supplementary. |
| **Include a link** | Every alert includes a direct link to take the primary action (approval checker, block explorer, etc.). |
| **Offer the deep dive** | Every alert ends with a `/clawback-token-check` or `/clawback-analyse` invocation for users who want the full picture. |
| **No "all clear" messages** | If a check cycle finds nothing, produce no output. Silence = safety. |
| **Multiple threats = multiple alerts** | If a single cycle finds 3 issues, send 3 separate alerts. Do not bundle unrelated threats. |

---

## Worked Examples

### Example 1: Risky Approval Alert

```
============================================================
CLAWBACK WATCHDOG ALERT
============================================================

WALLET: 0x1234...5678
CHAIN: BSC / BNB Chain (56)
THREAT: Unlimited USDT approval to flagged contract

------------------------------------------------------------
DETAILS
------------------------------------------------------------
Your wallet has an unlimited USDT approval to contract
0x7a3...f92, which has been flagged as high-risk by GoPlus
since your last check. This approval allows the contract to
spend your entire USDT balance without further permission.

------------------------------------------------------------
ACTION REQUIRED
------------------------------------------------------------
- Revoke this approval NOW: https://bscscan.com/tokenapprovalchecker
- Run /clawback-token-check 0x7a3...f92 for full contract analysis

============================================================
```

### Example 2: Honeypot Airdrop Alert

```
============================================================
CLAWBACK WATCHDOG ALERT
============================================================

WALLET: 0x1234...5678
CHAIN: BSC / BNB Chain (56)
THREAT: Suspicious token appeared in your wallet

------------------------------------------------------------
DETAILS
------------------------------------------------------------
A new token "SafeMoonX" (0xabc...def) appeared in your wallet
that you did not purchase. GoPlus flags it as a confirmed
honeypot with hidden mint function. This is a common airdrop
scam — interacting with it (even trying to sell) can trigger
malicious contract approvals.

------------------------------------------------------------
ACTION REQUIRED
------------------------------------------------------------
- DO NOT interact with this token. Do not try to sell or transfer it.
- If you see a "claim" site for this token, do not visit it.
- Run /clawback-token-check 0xabc...def for full token analysis

============================================================
```

### Example 3: Retroactive Flagging Alert

```
============================================================
CLAWBACK WATCHDOG ALERT
============================================================

WALLET: 0x1234...5678
CHAIN: BSC / BNB Chain (56)
THREAT: Previously-clean address now flagged as malicious

------------------------------------------------------------
DETAILS
------------------------------------------------------------
Address 0xdef...123, which your wallet interacted with on
approximately Feb 15, has been newly flagged by GoPlus for
phishing activity. Check if you granted any token approvals
to this address during that interaction.

------------------------------------------------------------
ACTION REQUIRED
------------------------------------------------------------
- Check your approvals: https://bscscan.com/tokenapprovalchecker
- If you have any approvals to 0xdef...123, revoke them immediately
- Run /clawback-token-check 0x1234...5678 to scan all your approvals

============================================================
```

---

## Registration Flow

Before registering, the skill checks OpenClaw memory configuration. If memoryFlush is enabled (default), warn:

```
⚠️ MEMORY PERSISTENCE REQUIRED
Watchdog needs persistent memory to track your wallet between
checks. Your current config clears memory on restart.

Fix (one-time, takes 10 seconds):
Add to ~/.openclaw/openclaw.json:
  "memory": {"memoryFlush": false, "persistenceMode": "hybrid"}

Then restart OpenClaw and run /clawback-watchdog again.
```

If memory persistence is confirmed, proceed with registration.

When a user runs `/clawback-watchdog 0xAddress`:

```
============================================================
CLAWBACK WATCHDOG — WALLET REGISTERED
============================================================

WALLET: 0xAddress
CHAIN: BSC / BNB Chain (56)
STATUS: Monitoring active
SCHEDULE: Every 8 hours
CRON JOB: Created — checking every 8 hours
  Job ID: [generated jobId]
  Test now: openclaw cron run [jobId]
  View runs: openclaw cron runs --id [jobId]

------------------------------------------------------------
INITIAL SCAN RESULTS
------------------------------------------------------------
[Run all available checks immediately on registration and
 report any current issues. This gives the user immediate
 value and establishes the baseline for future diff-based
 detection.]

Checks performed:
  Approvals:  ✅ scanned (GoPlus — no key needed)
  Airdrops:   ✅ scanned / ❓ skipped (no Etherscan key)
  Reputation: ✅ scanned (GoPlus — no key needed)

[If airdrop check was skipped:]
Note: Airdrop detection unavailable — Etherscan API key not
configured. Approval auditing and address reputation monitoring
are fully operational (GoPlus, no key needed). To enable
airdrop detection, add an Etherscan key:
  openclaw config set skills.entries.clawback-watchdog.env.ETHERSCAN_API_KEY "your-key"

[If no issues found:]
No threats detected. Your wallet is clean as of this scan.
This snapshot is now the baseline — future checks will alert
you to any changes.

[If issues found:]
[Output individual alerts for each finding using the standard
 alert format above.]

------------------------------------------------------------
WHAT HAPPENS NEXT
------------------------------------------------------------
- ClawBack will check this wallet every 8 hours
- You will ONLY hear from me if something needs your attention
- No news = good news
- To check status: /clawback-watchdog status
- To remove a wallet: /clawback-watchdog remove 0xAddress
- To list monitored wallets: /clawback-watchdog list

============================================================
```

The registration confirmation is the **only time** the skill is chatty. After this, silence = safety.

### Status Command Output

When a user runs `/clawback-watchdog status`:

```
============================================================
CLAWBACK WATCHDOG — STATUS
============================================================

MONITORED WALLETS: 2 of 5 maximum

WALLET 1: 0x1234...5678
  Chain:      BSC / BNB Chain (56)
  Registered: 2026-02-28
  Last check: 2026-03-05 06:15 UTC (2 hours ago)
  Next check: ~2026-03-05 14:15 UTC
  Status:     No active threats
  Checks:     Approvals ✅ | Airdrops ✅ | Reputation ✅

WALLET 2: 0xabcd...ef01
  Chain:      Ethereum (1)
  Registered: 2026-03-01
  Last check: 2026-03-05 06:15 UTC (2 hours ago)
  Next check: ~2026-03-05 14:15 UTC
  Status:     1 active alert (risky approval)
  Checks:     Approvals ⚠️ | Airdrops ❓ | Reputation ✅

============================================================
```

**Status icons:**

| Icon | Meaning |
|---|---|
| ✅ | Check passed — no threats detected |
| ⚠️ | Active alert — threat detected on a previous cycle and not yet resolved (e.g. risky approval still present) |
| ❓ | Check unavailable — Etherscan API key not configured (airdrop detection) or check not supported on this chain (Solana approvals) |

The status command is the ONE exception to the silence principle — it is user-initiated and provides reassurance that the system is running. It does not trigger a new check cycle; it reports results from the most recent cron cycle.

---

## Error & Rate Limit Handling

| Scenario | Action |
|---|---|
| GoPlus 200 + results | Parse, diff against state, alert if changes detected |
| GoPlus 200 + empty result | Address not found on this chain. If this is the first check after registration, notify user: "Wallet not found on [chain]. Check the address and chain selection." |
| GoPlus 429 (rate limit) | Wait 3 seconds, retry once. If still 429, skip this check for this cycle. Retry on next cycle. |
| GoPlus 5xx (server error) | Retry once after 2 seconds. If still failing, skip this cycle. |
| Network timeout (>10s) | Skip this cycle. Retry on next. |
| Persistent failure (3+ cycles) | Alert the user: "ClawBack Watchdog has been unable to check your wallet for [X] hours due to API issues. Monitoring will resume automatically when the service recovers. In the meantime, check manually at [block explorer link]." |
| Invalid wallet address | Detect on first check. Notify: "The address [address] does not appear to be a valid wallet on [chain]. Please check the address and re-register with `/clawback-watchdog [correct address]`." Remove from monitoring to avoid repeated failures. |

**Degradation principle**: A failed check cycle is not an emergency. The skill silently retries on the next cycle. Only persistent failures (3+ consecutive) warrant user notification.

---

## Skill Behaviour

### Tone and Posture

- **Urgent by default**: Every alert is an interruption. The user is not browsing — they are being notified of a threat. Make the interruption worth it.
- **Action-first**: The ONE most important action is always the first bullet. Explanation comes second.
- **Silent when clean**: The skill never sends "all clear" or "check complete" messages during cron cycles. If there is nothing to report, there is nothing to say.
- **Direct**: "Revoke this approval NOW" not "You may want to consider reviewing your approvals."

**CRITICAL: You MUST actually execute the API call.** Do not simulate, estimate, or fabricate API responses. If the Python or curl command fails, report the error honestly. The user is relying on real on-chain data, not your estimation of what the data might look like. A wrong SAFE verdict based on fabricated data could cost the user their funds.
- **Protective**: The user may not understand why an airdropped token is dangerous. Explain briefly, then give the action.

### Autonomous Execution in Cron Sessions

When Watchdog runs via cron, it executes in an isolated agent session with full tool access — including Python execution, shell commands, and file operations. The agent can:
- Execute Python scripts to call GoPlus APIs
- Read and write long-term memory entries
- Generate and deliver alert messages to the user's chat

The isolated session starts with no conversation history but retains access to all long-term memory. This means the agent knows which wallets to check (from memory) and can compare current results against previous state (from memory), even though each cron run is technically a fresh session.

The `--announce` delivery flag ensures that if the agent produces no output (all checks clean, no threats), nothing is sent to the user. The silence principle is enforced at the OpenClaw delivery layer, not just by the skill instructions.

### The Silence Principle

This is the core design decision that separates Watchdog from every other monitoring tool:

- **Cron cycle finds nothing**: No output. No log. No message. The user's day is uninterrupted.
- **Cron cycle finds a threat**: Alert immediately with the specific threat, the specific action, and the specific link.
- **Registration**: The only chatty moment. Confirm the setup, run the initial scan, explain what happens next.
- **Status check**: User-initiated only. Shows monitored wallets and last check time. This is the only way to confirm the watchdog is running without triggering a full cycle.

### When to Alert vs. When to Stay Silent

| Situation | Alert? | Why |
|---|---|---|
| New unlimited approval to unknown contract | Yes | Immediate risk — contract can drain approved tokens |
| New approval to previously-clean contract | No (until flagged) | Approval itself is normal. Alert only if the contract gets flagged later. |
| Honeypot token airdropped to wallet | Yes | User might interact with it, triggering further approvals |
| New token airdropped with no GoPlus flags | No | Could be legitimate. Record in state, check flags on future cycles. |
| Previously-clean address now flagged | Yes | User may have active approvals to this address |
| Address reputation unchanged (still clean) | No | Nothing changed |
| GoPlus API temporarily down | No (unless 3+ cycles) | Transient failures are not the user's problem |
| GoPlus API down for 3+ cycles | Yes | User should know monitoring is degraded |

### Edge Cases

- **Multiple wallets**: Each wallet is checked independently. Alerts are wallet-specific. A user with 5 registered wallets gets 5 independent check cycles (rate limits permitting).
- **Same address on multiple chains**: Supported. Register with `/clawback-watchdog 0xAddress ethereum` and `/clawback-watchdog 0xAddress bsc`. Each chain is tracked independently — a token can be a honeypot on BSC and not exist on Ethereum.
- **Wallet with hundreds of approvals**: The initial scan may produce many alerts. Group related findings (e.g. "You have 7 unlimited approvals — 2 are to flagged contracts") and lead with the most dangerous. Provide the approval checker link once, not seven times.
- **GoPlus down for multiple cycles**: After 3 consecutive failures, alert the user with the degradation notice. Resume silent monitoring when the API recovers. Do not re-alert once recovered — just start checking normally.
- **Solana wallets**: Token security check runs normally. Approval scanning and address reputation are skipped (EVM-only). State in the registration confirmation: "Solana monitoring covers token security only. Approval scanning is not available for Solana."
- **Wallet registered but user revoked all flagged approvals**: Next cycle detects the revocation (approval no longer present), updates state, stays silent. The threat was resolved — no need to confirm.
- **Max wallet limit reached**: "You are monitoring the maximum of 5 wallets. Remove a wallet with `/clawback-watchdog remove 0xAddress` before adding a new one. Configure the limit in `~/.openclaw/openclaw.json`."

---

## What This Skill Is NOT

- **Not a social engineering analyst** — use `clawback-analyse` for message, email, and screenshot analysis
- **Not a URL scanner** — use `clawback-url-scan` for domain and link inspection
- **Not a one-shot token checker** — use `clawback-token-check` for on-demand deep analysis of specific addresses
- **Not a reporting tool** — use `clawback-report` to generate formal incident reports

This skill is the **always-on security layer** — it watches your wallet around the clock and only speaks when something needs your attention. The other skills analyse what you bring to them. This one finds threats before you know they exist.

---

## Integration with ClawBack Suite

ClawBack Watchdog alerts naturally feed into the other skills. Every alert includes a handoff command for deeper analysis.

| Alert Type | Trigger Skill | What to Say in Alert |
|---|---|---|
| Risky approval detected | `clawback-token-check` | "Run `/clawback-token-check [contract_address]` for full contract analysis." |
| Honeypot airdrop detected | `clawback-token-check` | "Run `/clawback-token-check [token_address]` for full token analysis." |
| Address retroactively flagged | `clawback-token-check` | "Run `/clawback-token-check [your_wallet]` to scan all your approvals." |
| Alert links to a suspicious URL or site | `clawback-url-scan` | "Run `/clawback-url-scan [url]` if the token or contract references a website." |
| User wants to understand the social engineering behind the threat | `clawback-analyse` | "Run `/clawback-analyse` and paste any messages you received about this token or contract." |
| User needs a formal incident report | `clawback-report` | "Run `/clawback-report` to generate a formal incident report for law enforcement or exchange support." |
| User wants to practice recognising these threats | `clawback-train` | "Run `/clawback-train` for interactive training on spotting crypto scam patterns." |
| User wants to understand the concepts behind an alert | `clawback-learn` | "Want to understand what [term] means? Run `/clawback-learn [term]` for a plain-English explanation." |

---

## About the Builder

**Vance Lever** ([@LeverCRO](https://x.com/LeverCRO)) — AI CRO and agentic economy builder with deep expertise in social engineering, manipulation detection, and crypto security. ClawBack is built on the principle that the best defence against social engineering is understanding exactly how it works — naming the weapons, mapping the playbook, and breaking the emotional spell that makes intelligent people hand over their keys.
