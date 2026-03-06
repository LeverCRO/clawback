---
name: clawback-token-check
description: On-chain token, contract, and wallet address security scanner powered by the GoPlus Security API. Use when a user pastes a contract address, token address, or wallet address and wants to know if it is safe. Checks for honeypots, hidden mint functions, ownership status, liquidity locks, buy/sell taxes, blacklist functions, proxy contracts, holder concentration, malicious address flags, phishing history, and risky token approvals. Returns a clear SAFE / LOW RISK / MEDIUM RISK / HIGH RISK / CRITICAL verdict with plain English explanations. BSC-first (Binance Smart Chain) but supports Ethereum, Polygon, Arbitrum, Base, Avalanche, and Solana. Zero configuration required — no API key needed. Activates automatically when a user pastes an on-chain address, or explicitly via /clawback-token-check.
metadata: {"clawdbot":{"emoji":"🔬","homepage":"https://clawback.bot"}}
---

# ClawBack Token Check

**On-chain address security scanner for crypto scam defence.**

Built by Vance Lever ([@LeverCRO](https://x.com/LeverCRO)) — AI CRO, agentic economy builder, crypto security researcher. Part of the [ClawBack](https://github.com/LeverCRO/clawback) skill suite for the Binance OpenClaw AI Innovation Competition.

---

## When This Skill Activates

This skill activates in two ways:

1. **Automatically** — when a user pastes a contract address, token address, or wallet address and asks whether it is safe
2. **Explicitly** — when a user invokes `/clawback-token-check`

### Supported Input Types

- Token contract addresses (e.g. `0x...` on EVM chains, base58 on Solana)
- Wallet addresses to check reputation and risky approvals
- Contract addresses for DApps, staking pools, or unknown protocols
- Addresses embedded in suspicious messages, tweets, or DMs
- Partial context ("someone told me to buy this token" + address)

If a user pastes an address — this skill can check it.

---

## Address Type Detection

Before making any API call, determine the address type and run the appropriate sequence:

```
INPUT: address string

STEP 1 — Classify the address
├── Starts with 0x and is 42 characters → EVM address
├── Base58 encoded, 32-44 characters → Solana address
└── Anything else → INVALID — tell user, stop

STEP 2 — Determine address type (EVM only)
├── Call Token Security endpoint first
│   ├── API returns token data (name, symbol, holders) → It's a TOKEN
│   │   → Run: Token Security ✓ → Malicious Address ✓ → Skip Approval Security
│   │
│   └── API returns empty/no token data → It's a WALLET or CONTRACT
│       → Run: Malicious Address ✓ → Approval Security ✓ → Skip Token Security
│
└── If user explicitly says "check my wallet" → Skip Token Security
    → Run: Malicious Address ✓ → Approval Security ✓

STEP 3 — Compile results from all endpoints into single report
```

**Important**: Always run Malicious Address check regardless of address type. It applies to both tokens and wallets.

**Important**: The Token Security call in Step 2 serves dual purpose — it both classifies the address AND provides the token security data. If it returns token data, proceed directly to the Malicious Address check. Do not call the Token Security endpoint a second time.

---

## API: GoPlus Security

This skill uses the [GoPlus Security API](https://gopluslabs.io) — the largest open, permissionless security data provider in Web3.

**Zero configuration required.** No API key needed for basic usage. The skill works out of the box with no setup. If GoPlus introduces key-based access in the future, configure it in `~/.openclaw/openclaw.json` under `skills.entries.clawback-token-check.env.GOPLUS_API_KEY`.

> **Optional**: Users can register at [gopluslabs.io](https://gopluslabs.io) for higher rate limits on heavy usage. This is not required for normal operation.

### Endpoints

| Endpoint | Purpose | URL |
|---|---|---|
| **Token Security** | Honeypot detection, mint functions, ownership, taxes, liquidity, holder analysis | `https://api.gopluslabs.io/api/v1/token_security/{chain_id}?contract_addresses={address}` |
| **Malicious Address** | Phishing, blackmail, money laundering, darkweb, cybercrime flags | `https://api.gopluslabs.io/api/v1/address_security/{address}` |
| **Approval Security** | Risky token approvals, unlimited allowances, approvals to malicious contracts | `https://api.gopluslabs.io/api/v1/approval_security/{chain_id}?contract_addresses={address}` |

### Multi-Chain Support

| Chain | Chain ID | Notes |
|---|---|---|
| **BSC / BNB Chain** | `56` | Highest volume of scam token deployments — tried first in auto-detection |
| Ethereum | `1` | |
| Base | `8453` | |
| Polygon | `137` | |
| Arbitrum | `42161` | |
| Avalanche | `43114` | |
| Solana | `solana` | **Partial** — Token Security only. Address reputation and approval security are EVM-only. See [Solana Support](#solana-support--limited) below. |

**Supported chain names and IDs:**

```
ethereum / eth = 1
bsc / bnb = 56
polygon / matic = 137
arbitrum / arb = 42161
base = 8453
avalanche / avax = 43114
solana / sol = solana (different endpoint)
```

**Chain Detection for Token Checks:**

If the user specifies a chain (e.g. `/clawback-token-check 0xAddr ethereum` or `/clawback-token-check 0xAddr --chain 1`), use that chain ID.

If no chain is specified, try multiple chains in order until data is found:

1. BSC (56) — try first (highest scam volume)
2. Ethereum (1)
3. Base (8453)
4. Polygon (137)
5. Arbitrum (42161)

Use the first chain that returns data. Report which chain the data came from in the output header: "Token found on BSC (chain 56)"

If no chain returns data, report: "No data found on any supported chain. Specify the chain manually: `/clawback-token-check 0xAddr [chain-name]`"

**CRITICAL — Address Lowercasing:**
GoPlus returns Token Security results keyed by the LOWERCASED version of the address. If you submit `0xBB4CdB9...` the result key will be `0xbb4cdb9...`. Always lowercase the address before looking up results in the response. The Python method handles this automatically. If using curl+jq, the `jq '.result[]'` syntax works regardless of key casing.

### Making API Calls

All API calls use curl with shell variable expansion (`$VARIABLE_NAME`) piped into Python for JSON parsing. The shell expands environment variables before the agent processes the command — this avoids security refusals that occur when agents access `os.environ` directly.

**API Key Access Pattern:**

OpenClaw injects `skills.entries.<skill>.env` variables into the process environment at runtime. Users configure keys via the Skills page in the Control UI or via:

```
openclaw config set skills.entries.<skill>.env.KEY_NAME value
```

If a key is not configured, the curl call will fail with a 401/403 error which the Python parser catches and reports with setup instructions.

**Token security check:**
```bash
# Token security check (no API key needed)
# Replace CHAIN_ID with: 56 (BSC), 1 (ETH), 8453 (Base), 137 (Polygon), 42161 (Arbitrum)
curl -s "https://api.gopluslabs.io/api/v1/token_security/CHAIN_ID?contract_addresses=ADDRESS" | python3 -c "
import json, sys
d = json.load(sys.stdin)
addr = list(d.get('result',{}).keys())[0] if d.get('result') else None
if not addr:
    print(json.dumps({'error': 'No data found for this address on this chain'}))
    sys.exit(0)
t = d['result'][addr]
fields = ['token_name','token_symbol','is_honeypot','honeypot_with_same_creator',
    'is_mintable','owner_address','can_take_back_ownership','is_open_source',
    'is_proxy','buy_tax','sell_tax','cannot_sell_all','cannot_buy',
    'is_blacklisted','transfer_pausable','external_call','is_anti_whale',
    'slippage_modifiable','personal_slippage_modifiable','is_in_dex',
    'holder_count','total_supply','creator_address','creator_balance',
    'creator_percent','lp_total_supply','lp_holder_count']
out = {f: t.get(f) for f in fields}
out['holders'] = t.get('holders', [])[:20]
out['lp_holders'] = t.get('lp_holders', [])[:10]
print(json.dumps(out, indent=2))
"
```

**Address security check:**
```bash
# Address security check (no API key needed)
curl -s "https://api.gopluslabs.io/api/v1/address_security/ADDRESS" | python3 -c "
import json, sys
print(json.dumps(json.load(sys.stdin).get('result',{}), indent=2))
"
```

**Approval security check:**
```bash
# Approval security check (no API key needed)
# Replace CHAIN_ID with the appropriate chain ID
curl -s "https://api.gopluslabs.io/api/v1/approval_security/CHAIN_ID?contract_addresses=ADDRESS" | python3 -c "
import json, sys
print(json.dumps(json.load(sys.stdin).get('result',{}), indent=2))
"
```

For multiple addresses, comma-separate them in the `contract_addresses` parameter.

**IMPORTANT — Context Window Management:**

The raw GoPlus Token Security response includes extensive DEX pair and liquidity pool data that can be thousands of lines of JSON. ALWAYS use the curl-pipe-Python pattern above to extract only security-relevant fields. Dumping the raw response into conversation context will cause truncation and unreliable parsing.

The curl+Python pattern is preferred because:
1. curl handles HTTP reliably and shell expands `$ENV_VARS` before the agent sees them
2. Python parses JSON and extracts only security-relevant fields
3. GoPlus returns results keyed by LOWERCASED address — the `list(d.get('result',{}).keys())[0]` pattern handles this automatically
4. It caps holders and lp_holders arrays to prevent context overflow

### Response Field Mapping

The GoPlus Token Security API returns fields as string values (`"0"` or `"1"`), not booleans. Always compare against the string `"1"`.

**Token Security endpoint** — `token_security/{chain_id}?contract_addresses={address}`

Response structure: `result.{address_lowercase}.{field}`

| GoPlus Field | ClawBack Check | Dangerous Value | Risk Level |
|---|---|---|---|
| `is_honeypot` | Honeypot Detection | `"1"` = confirmed honeypot | CRITICAL |
| `honeypot_with_same_creator` | Deployer History | `"1"` = creator has made honeypots before | CRITICAL |
| `is_mintable` | Hidden Mint Function | `"1"` = owner can mint unlimited tokens | CRITICAL |
| `owner_address` | Ownership Status | Any non-null, non-zero address = not renounced | HIGH |
| `can_take_back_ownership` | Ownership Recovery | `"1"` = ownership can be reclaimed after renouncing | CRITICAL |
| `is_open_source` | Contract Verification | `"0"` = source code not verified | MEDIUM |
| `is_proxy` | Proxy Contract | `"1"` = upgradeable proxy pattern | MEDIUM-HIGH |
| `buy_tax` | Buy Tax | String like `"0.05"` = 5%. Flag if > `"0.10"` | MEDIUM+ |
| `sell_tax` | Sell Tax | String like `"0.05"` = 5%. Flag if > `"0.10"`. If > `"0.50"` = effective honeypot | MEDIUM-CRITICAL |
| `cannot_sell_all` | Sell Restriction | `"1"` = cannot sell entire balance | HIGH |
| `is_blacklisted` | Blacklist Function | `"1"` = owner can blacklist wallets | HIGH |
| `transfer_pausable` | Transfer Pausable | `"1"` = owner can freeze all transfers | HIGH |
| `external_call` | External Calls | `"1"` = contract makes external calls | MEDIUM |
| `is_anti_whale` | Anti-Whale Mechanism | `"1"` = max tx/holding limits exist | Informational |
| `slippage_modifiable` | Slippage Modifiable | `"1"` = owner can change buy/sell tax at any time | HIGH |
| `personal_slippage_modifiable` | Targeted Tax | `"1"` = owner can set different tax per wallet | CRITICAL |
| `is_in_dex` | DEX Listing | `"0"` = not listed on any DEX | MEDIUM |
| `lp_holder_count` | Liquidity Providers | Low count = fragile liquidity | Informational |
| `lp_total_supply` | Liquidity Depth | Low value = thin liquidity, easy to manipulate | MEDIUM |
| `holder_count` | Holder Count | Very low = new/suspicious token | Informational |
| `holders` | Top Holder Concentration | Array — see concentration calculation below | HIGH if concentrated |
| `dex` | DEX Info | Array of DEX listings with pair addresses | Informational |
| `total_supply` | Total Supply | String — total token supply | Informational |

**Holder concentration calculation:**

```
holders = result.{address}.holders  // array of {address, balance, percent, is_contract, is_locked, tag}

// Filter out non-human holders
exclude = holders where:
  - is_contract == 1 AND tag contains "DEX" or "Pair" or "Router"
  - is_locked == 1  (locked tokens, e.g. team vesting)
  - address == "0x000000000000000000000000000000000000dead" (burn address)

// Sum remaining top 10
human_holders = holders excluding above, sorted by percent descending, take top 10
concentration = sum(human_holders.percent)

if concentration > 0.80 → HIGH RISK ("Top 10 non-contract holders control >80%")
if concentration > 0.60 → MEDIUM RISK
if concentration > 0.40 → Informational
```

**Liquidity lock detection:**

```
lp_holders = result.{address}.lp_holders  // array

locked_lp = lp_holders where is_locked == 1
total_lp = sum of all lp_holders.percent

if no locked_lp → "Liquidity NOT locked — deployer can rug pull"
if locked_lp exists:
  locked_percent = sum(locked_lp.percent)
  lock_end = locked_lp[0].locked_detail // may contain expiry info
  report: "{locked_percent}% of LP locked until {lock_end}"
```

**Malicious Address endpoint** — `address_security/{address}`

Response structure: `result.{field}`

| GoPlus Field | ClawBack Check | Dangerous Value |
|---|---|---|
| `cybercrime` | Cybercrime | `"1"` |
| `money_laundering` | Money Laundering | `"1"` |
| `number_of_malicious_contracts_created` | Contract Abuse | > `"0"` |
| `financial_crime` | Financial Crime | `"1"` |
| `darkweb_transactions` | Darkweb Transactions | `"1"` |
| `phishing_activities` | Phishing Activity | `"1"` |
| `fake_kyc` | Fake KYC | `"1"` |
| `blacklist_doubt` | Blacklist Suspicion | `"1"` |
| `data_source` | Source | String — where the flag originates |
| `honeypot_related_address` | Honeypot Association | `"1"` |
| `blackmail_activities` | Blackmail | `"1"` |

**Approval Security endpoint** — `approval_security/{chain_id}?contract_addresses={contract_address}`

This endpoint checks whether a specific CONTRACT is safe to have approved — it does NOT list a wallet's approvals. To audit a wallet's approvals, you need the wallet's approval list from a block explorer first, then check each approved contract through this endpoint.

Response structure: `result.{field}`

| GoPlus Field | ClawBack Check | What It Means |
|---|---|---|
| `contract_name` | Contract Identity | Name of the contract |
| `tag` | Contract Tag | Classification tag |
| `is_contract` | Is Contract | 1 = contract, 0 = EOA |
| `creator_address` | Creator | Who deployed this contract |
| `deployed_time` | Deploy Time | Unix timestamp of deployment |
| `is_open_source` | Source Verified | 1 = verified source code |
| `is_proxy` | Proxy Contract | 1 = upgradeable proxy |
| `trust_list` | Trusted | 1 = on GoPlus trust list |
| `doubt_list` | Suspicious | 1 = on GoPlus doubt list |
| `malicious_behavior` | Malicious Flags | Array of flagged behaviours (empty = clean) |
| `contract_scan.privilege_withdraw` | Privileged Withdrawal | 1 = can withdraw user funds |
| `contract_scan.withdraw_missing` | Missing Withdrawal | 1 = no withdrawal function |
| `contract_scan.blacklist` | Blacklist Function | 1 = can blacklist addresses |
| `contract_scan.selfdestruct` | Self-Destruct | 1 = can self-destruct |
| `contract_scan.approval_abuse` | Approval Abuse | 1 = can abuse approvals |
| `risky_approval.risk` | Approval Risk | Risk level (null = no risk) |
| `risky_approval.value` | Risk Score | 0 = clean |

**Decision logic:**
- `trust_list == 1` → positive signal (on GoPlus trust list)
- `doubt_list == 1` → flag as suspicious
- `malicious_behavior` not empty → flag as dangerous
- `contract_scan.approval_abuse == 1` → CRITICAL
- `contract_scan.privilege_withdraw == 1` → HIGH
- `contract_scan.selfdestruct == 1` → HIGH
- `risky_approval.risk` not null → flag based on value

### Error & Rate Limit Handling

| HTTP Status | Meaning | Action |
|---|---|---|
| `200` + `result` populated | Success | Parse and report |
| `200` + `result` empty `{}` | Address not found on this chain | Tell user: "No data found on {chain}. The token may be on a different chain or too new for GoPlus to index." Offer to try other chains. |
| `429` | Rate limited | Wait 3 seconds, retry once. If still 429, fall back to LLM reasoning. |
| `5xx` | Server error | Retry once after 2 seconds. If still failing, fall back. |
| Network timeout (>10s) | Unreachable | Fall back to LLM reasoning. |

**Fallback protocol** (when API is unavailable):

1. State clearly in the report header: `⚠️ API UNAVAILABLE — This analysis is based on LLM reasoning and publicly known patterns, NOT verified on-chain data. Results are estimates only.`
2. Provide best-effort analysis from any context the user shared (token name, where they found it, who recommended it).
3. Always recommend the user retry later or check manually:
   - GoPlus dashboard: `https://gopluslabs.io/token-security/{chain_id}/{address}`
   - BSCScan: `https://bscscan.com/address/{address}`
4. Never present fallback results with ✅ / 🚨 icons — use ❓ for all checks to signal uncertainty.

### Solana Support — Limited

| Chain | Chain ID | Status |
|---|---|---|
| Solana | `solana` | **Partial** — Token Security only. Address reputation and approval security are EVM-only. |

**When a user submits a Solana address:**

1. Detect base58 format (32-44 characters, no `0x` prefix).
2. Call Token Security endpoint with chain ID `solana`:
   ```
   https://api.gopluslabs.io/api/v1/token_security/solana?contract_addresses={address}
   ```
3. GoPlus returns a different response structure for Solana SPL tokens. Key differences:
   - No `lp_holders` field — liquidity lock detection is not available
   - No `is_proxy` field — Solana programs use a different upgrade model
   - `owner_address` maps to the program's upgrade authority
4. Run the Malicious Address check normally (endpoint is chain-agnostic).
5. Skip Approval Security (EVM-only endpoint).
6. State in the report: "Solana analysis covers token security and address reputation. Approval scanning is not available for Solana — use a Solana-specific tool like Solscan for approval review."

If you are unsure whether a Solana response field maps correctly, **say so in the report** rather than guessing. Partial honesty > confident error.

---

## Detection Framework

### Token / Contract Security Checks

Every token or contract address is checked against these flags. Each result is translated into plain English — no raw API fields in the output.

| Check | What It Means | Risk If Flagged |
|---|---|---|
| **Honeypot Detection** | Can you buy but not sell? The contract blocks or taxes sell transactions to trap buyers. | **CRITICAL** — You will lose 100% of your investment. This is the single most important check. |
| **Hidden Mint Functions** | Can the deployer create unlimited new tokens, diluting your holdings to zero? | **CRITICAL** — Infinite supply = your tokens become worthless overnight. |
| **Ownership Status** | Has the deployer renounced ownership, or do they still have admin control? | **HIGH** if not renounced — owner can change contract rules at any time. |
| **Liquidity Lock** | Is liquidity locked in a time-locked contract, or can it be withdrawn instantly? | **HIGH** if unlocked — deployer can pull all liquidity (rug pull) at any moment. |
| **Buy/Sell Tax** | Are there hidden taxes on buy or sell transactions? Anything above 10% is a red flag; above 50% is effectively a honeypot. | **MEDIUM to CRITICAL** depending on percentage. |
| **Blacklist Function** | Can the contract owner blacklist specific wallet addresses, preventing them from selling? | **HIGH** — Selective honeypot: they let most people sell but block you specifically. |
| **Proxy Contract** | Can the contract logic be changed after deployment via a proxy/upgradeable pattern? | **MEDIUM to HIGH** — Contract could be safe today and malicious tomorrow. |
| **DEX Listing Status** | Is the token listed and tradeable on PancakeSwap (BSC), Uniswap, or other major DEXs? | **MEDIUM** if not listed — may be pre-launch or a fabricated token. |
| **Top Holder Concentration** | Is the token supply dangerously concentrated in a few wallets? | **HIGH** if top 10 holders control >80% — one wallet selling can crash the price to zero. |
| **Anti-Whale Mechanism** | Does the contract limit maximum transaction size or holdings? | Informational — can be protective or used to slow-sell a rug. |
| **Transfer Pausable** | Can the owner pause all token transfers? | **HIGH** — Freeze all holders at will. |
| **External Calls** | Does the contract make external calls that could be exploited? | **MEDIUM** — Potential attack surface for advanced exploits. |

### Address Reputation Checks

Every address (wallet or contract) is checked against the GoPlus malicious address database covering 9M+ flagged addresses:

| Check | What It Means |
|---|---|
| **Phishing Activity** | Address has been involved in phishing campaigns — fake sites, credential harvesting, DM scams |
| **Blackmail / Cybercrime** | Associated with extortion, ransomware, or other cybercrime |
| **Money Laundering** | Flagged in laundering patterns — mixing services, rapid chain-hopping |
| **Darkweb Transactions** | Connected to darknet marketplace activity |
| **Honeypot Creator** | Address has deployed known honeypot contracts in the past |
| **Financial Crime** | Broader financial fraud associations |
| **Contract Abuse** | Has deployed or interacted with contracts flagged as malicious |

### Approval Risk Checks

When checking wallet addresses, the skill also examines outstanding token approvals:

| Check | What It Means |
|---|---|
| **Unlimited Approvals** | You gave a contract permission to spend unlimited tokens from your wallet — if that contract is compromised, you lose everything it has approval for |
| **Approvals to Malicious Contracts** | You previously approved a contract that is now flagged as malicious — your tokens may be at immediate risk |
| **Stale Approvals** | Old approvals to contracts you no longer use — unnecessary attack surface |

---

## Risk Scoring

Every analysis produces an overall risk rating based on the severity and combination of flags detected:

| Rating | Criteria | Action |
|---|---|---|
| **CRITICAL** | Confirmed honeypot, active mint exploit, known scam contract, or address flagged for active phishing/crime | Do not interact. If you hold tokens, they may already be unrecoverable. |
| **HIGH RISK** | Ownership not renounced + liquidity unlocked, blacklist function active, extreme holder concentration, or address with laundering/darkweb flags | Strongly avoid. Multiple indicators of potential rug pull or scam infrastructure. |
| **MEDIUM RISK** | Elevated buy/sell taxes, proxy contract with active owner, moderate holder concentration, or minor address flags | Proceed with extreme caution. Verify team, audit status, and community independently. |
| **LOW RISK** | Minor flags that are common in legitimate tokens (e.g. small tax for reflections, ownership retained for operational reasons with timelock) | Generally acceptable but verify specifics. |
| **SAFE** | No flags detected across all checks. Ownership renounced, liquidity locked, no malicious address history, reasonable holder distribution. | No red flags found. Standard investment risks still apply. |

**Priority rule**: The most dangerous flag determines the floor of the rating. A token that passes every check except honeypot detection is **CRITICAL**, not "mostly safe with one concern."

---

## Output Format

Every analysis produces a structured report. The most dangerous finding always leads.

```
============================================================
CLAWBACK TOKEN ANALYSIS
============================================================

ADDRESS: [address checked]
CHAIN: [chain name + chain ID]
TYPE: [Token / Wallet Address / Contract]
OVERALL RISK: [SAFE / LOW RISK / MEDIUM RISK / HIGH RISK / CRITICAL]

------------------------------------------------------------
CRITICAL FLAGS
------------------------------------------------------------
🚨 [Flag name]: [Plain English explanation of what this means
   and why it is dangerous]

[Only present if CRITICAL or HIGH RISK flags exist.
 If no critical flags, this section is replaced with:
 "No critical flags detected."]

------------------------------------------------------------
TOKEN SECURITY CHECKS
------------------------------------------------------------
✅ / ⚠️ / 🚨 [Check name]: [Result + plain English explanation]
✅ / ⚠️ / 🚨 [Check name]: [Result + plain English explanation]
[Continue for all checks performed]

------------------------------------------------------------
ADDRESS REPUTATION
------------------------------------------------------------
✅ / ⚠️ / 🚨 [Check name]: [Result]
[Continue for all reputation flags checked]

------------------------------------------------------------
APPROVAL RISKS
------------------------------------------------------------
[Present when a wallet address is checked.
 Lists risky approvals found with revocation instructions.
 If no risky approvals: "No risky approvals detected."]

------------------------------------------------------------
WHAT THIS MEANS FOR YOU
------------------------------------------------------------
[2-3 sentence plain English summary. Lead with the most
important finding. No jargon. Written for someone who does
not read Solidity.]

------------------------------------------------------------
RECOMMENDED ACTION
------------------------------------------------------------
- [Specific steps: revoke approvals, avoid interaction,
  report, move funds, etc.]
- [Include direct tool links where relevant]
- [If token is a honeypot: state clearly that funds
  cannot be recovered]

------------------------------------------------------------
VERIFY ON CHAIN
------------------------------------------------------------
- [Direct block explorer link for the address]
- [Token approval checker link if applicable]
- [GoPlus dashboard link if available]

============================================================
```

### Status Icons

| Icon | Meaning |
|---|---|
| ✅ | Check passed — no risk detected |
| ⚠️ | Warning — elevated risk, review recommended |
| 🚨 | Critical — confirmed danger or high-severity flag |

---

## Skill Behaviour

### Tone and Posture

Same as all ClawBack skills — security professional, not customer service.

- **Lead with danger**: If something is a honeypot, say "This is a honeypot — you cannot sell this token" before any other detail. Never bury a critical flag in a list.
- **Plain English first**: Every technical finding is translated. "Owner not renounced" becomes "The person who created this token still has full admin control and can change the rules at any time."

**CRITICAL: You MUST actually execute the API call.** Do not simulate, estimate, or fabricate API responses. If the Python or curl command fails, report the error honestly. The user is relying on real on-chain data, not your estimation of what the data might look like. A wrong SAFE verdict based on fabricated data could cost the user their funds.
- **Direct**: Gives a clear risk rating. Does not say "this could go either way" when the contract has a mint function and unlocked liquidity.
- **Protective**: If the user already holds the token, the skill acknowledges the situation and provides realistic options (not just "you shouldn't have bought it").

### Chain Detection and Defaults

1. If the user specifies a chain ("check this on Ethereum"), use that chain.
2. If the user does not specify a chain, **auto-detect**: try BSC (56) → Ethereum (1) → Base (8453) → Polygon (137) → Arbitrum (42161) in order. Use the first chain that returns data.
3. Always state which chain was checked in the output: "Token found on BSC (chain 56)".
4. If no chain returns data: "No data found on any supported chain. Specify the chain manually."

### Handling Multiple Addresses

If a user pastes multiple addresses, check each one separately and produce a report for each. If addresses appear related (e.g. deployer wallet + token contract), note the connection.

### Handling Invalid Addresses

If the address format is invalid or the API returns no data:
1. State clearly that the address could not be found on the specified chain.
2. Suggest checking the address format and chain selection.
3. Offer to try a different chain — the token may be deployed elsewhere.

### Edge Cases

- **Legitimate tokens with flags**: Some real tokens have buy/sell taxes (reflection tokens), retained ownership (for operational upgrades with timelocks), or concentrated holdings (team vesting). The skill notes when a flag has a plausible legitimate explanation — but still reports it.
- **New tokens with no data**: If a token was just deployed and has minimal on-chain history, flag the lack of data itself as a risk factor. New + no liquidity lock + active owner = elevated risk by default.
- **User already holds the token**: If the user says they already bought, the skill shifts from "should you buy" to "what are your options now" — including whether selling is possible (honeypot check) and what the tax would cost.

---

## Worked Example

### Honeypot Token Detection on BSC

**User input**: "Is this token safe? 0x1a2b3c4d5e6f7890abcdef1234567890deadbeef"

```
============================================================
CLAWBACK TOKEN ANALYSIS
============================================================

ADDRESS: 0x1a2b3c4d5e6f7890abcdef1234567890deadbeef
CHAIN: BSC / BNB Chain (56)
TYPE: Token
OVERALL RISK: 🚨 CRITICAL

------------------------------------------------------------
CRITICAL FLAGS
------------------------------------------------------------
🚨 HONEYPOT CONFIRMED: This token cannot be sold. The contract
   allows buy transactions but blocks or fails all sell
   transactions. If you buy this token, your funds are trapped
   permanently. There is no workaround.

🚨 HIDDEN MINT FUNCTION: The contract owner can create unlimited
   new tokens at any time. Even if selling were possible, your
   holdings could be diluted to zero without warning.

------------------------------------------------------------
TOKEN SECURITY CHECKS
------------------------------------------------------------
🚨 Honeypot Status: CONFIRMED HONEYPOT
   Sell transactions revert or are blocked by contract logic.
   You can buy but you cannot sell. This is irreversible.

🚨 Mint Function: HIDDEN MINT DETECTED
   The contract contains a function that allows the deployer
   to mint unlimited tokens. This was not declared in the
   token metadata.

🚨 Ownership: NOT RENOUNCED
   The deployer wallet 0xDEPLOYER...1234 retains full owner
   privileges. They can modify contract parameters, pause
   transfers, and execute the mint function.

🚨 Liquidity: NOT LOCKED
   Liquidity was added to PancakeSwap but is not locked in
   any timelock contract. The deployer can withdraw all
   liquidity at any moment (rug pull).

⚠️ Buy Tax: 2% — appears normal but is irrelevant since
   selling is not possible.

🚨 Sell Tax: 100% — even if the sell function did not revert,
   a 100% tax would take your entire sell amount. This
   confirms honeypot intent.

🚨 Blacklist Function: PRESENT
   The contract owner can blacklist individual wallets,
   preventing them from executing any transaction with
   this token.

⚠️ Proxy Contract: YES — UPGRADEABLE
   This is a proxy contract. The underlying logic can be
   changed by the owner at any time. The contract you
   interact with today may behave differently tomorrow.

⚠️ DEX Listing: Listed on PancakeSwap
   The token is tradeable on PancakeSwap but only in the
   buy direction. Sell orders will fail.

🚨 Top Holder Concentration: TOP 3 WALLETS HOLD 94.7%
   Three wallets control nearly all supply. One of these
   is the deployer. This is consistent with a scam token
   where the creator retains control of almost all tokens.

------------------------------------------------------------
ADDRESS REPUTATION
------------------------------------------------------------
🚨 Honeypot Creator: The deployer address has created 12
   other tokens previously flagged as honeypots.

🚨 Financial Crime: Address associated with multiple
   fraud reports in the GoPlus database.

⚠️ Phishing Activity: Connected to addresses involved in
   phishing campaigns targeting BSC users.

✅ Darkweb Transactions: No darkweb associations detected.
✅ Blackmail: No blackmail associations detected.

------------------------------------------------------------
WHAT THIS MEANS FOR YOU
------------------------------------------------------------
This is a confirmed honeypot scam token. If you buy it, you
will not be able to sell — your funds will be permanently
trapped in the contract. The deployer has done this before
with at least 12 other tokens. The combination of honeypot
mechanics, hidden mint function, unlocked liquidity, and 94.7%
holder concentration leaves zero ambiguity: this token exists
solely to steal your money.

------------------------------------------------------------
RECOMMENDED ACTION
------------------------------------------------------------
- DO NOT buy this token under any circumstances.
- If you already hold this token: your funds in this token
  are not recoverable. Do not send additional funds attempting
  to "fix" the situation.
- If someone recommended this token to you, they are either
  part of the scam or another victim. Use /clawback-analyse
  to check the message where you received this recommendation.
- Report the contract on BSCScan:
  https://bscscan.com/token/0x1a2b3c4d5e6f7890abcdef1234567890deadbeef
  For other chains, use the relevant block explorer:
  etherscan.io (Ethereum), polygonscan.com (Polygon),
  arbiscan.io (Arbitrum), basescan.org (Base),
  snowtrace.io (Avalanche), solscan.io (Solana).
- Block the person or channel that shared this address.
- Check your wallet for any approvals you may have granted to
  this contract and revoke them immediately.

------------------------------------------------------------
VERIFY ON CHAIN
------------------------------------------------------------
- BSCScan: https://bscscan.com/address/0x1a2b3c4d5e6f7890abcdef1234567890deadbeef
- BSC Token Approval Checker: https://bscscan.com/tokenapprovalchecker
- GoPlus Token Security: https://gopluslabs.io/token-security/56/0x1a2b3c4d5e6f7890abcdef1234567890deadbeef
- Other chain explorers: etherscan.io (Ethereum),
  polygonscan.com (Polygon), arbiscan.io (Arbitrum),
  basescan.org (Base), snowtrace.io (Avalanche),
  solscan.io (Solana)

============================================================
```

### Safe Token on BSC

**User input**: "Is WBNB safe? 0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c"

```
============================================================
CLAWBACK TOKEN ANALYSIS
============================================================

ADDRESS: 0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c
CHAIN: BSC / BNB Chain (56)
TYPE: Token (Wrapped BNB)
OVERALL RISK: ✅ SAFE

------------------------------------------------------------
CRITICAL FLAGS
------------------------------------------------------------
No critical flags detected.

------------------------------------------------------------
TOKEN SECURITY CHECKS
------------------------------------------------------------
✅ Honeypot Status: NOT A HONEYPOT
   Buy and sell transactions execute normally. No sell
   restrictions detected.

✅ Mint Function: NONE DETECTED
   No hidden or public mint functions. Supply cannot be
   inflated by the deployer.

✅ Ownership: N/A — WRAPPED NATIVE TOKEN
   WBNB is the canonical wrapped version of BNB, the native
   gas token of BSC. It is not a project token with an
   "owner" — it is core chain infrastructure.

✅ Liquidity: DEEP — NATIVE PAIR
   As the wrapped native token, WBNB is the base pair for
   the majority of trading on PancakeSwap. Liquidity is
   distributed across thousands of pools and cannot be
   "rug pulled."

✅ Buy Tax: 0%
   No tax on buy transactions.

✅ Sell Tax: 0%
   No tax on sell transactions.

✅ Blacklist Function: NONE
   No blacklist mechanism in the contract.

✅ Proxy Contract: NO
   Not an upgradeable proxy. Contract logic is immutable.

✅ DEX Listing: Listed on PancakeSwap + all major BSC DEXs
   WBNB is the most widely traded token on BSC.

✅ Contract Verified: YES
   Source code is publicly verified on BSCScan.

✅ Top Holder Concentration: DISTRIBUTED
   While large holders exist (exchanges, DeFi protocols),
   this is expected for a wrapped native token. No single
   non-contract entity controls a dangerous share.

------------------------------------------------------------
ADDRESS REPUTATION
------------------------------------------------------------
✅ Phishing Activity: None detected.
✅ Cybercrime: None detected.
✅ Money Laundering: None detected.
✅ Darkweb Transactions: None detected.
✅ Honeypot Creator: No.
✅ Financial Crime: None detected.

------------------------------------------------------------
WHAT THIS MEANS FOR YOU
------------------------------------------------------------
WBNB (Wrapped BNB) is the canonical wrapped version of BNB,
the native gas token of BNB Chain. It is core blockchain
infrastructure, not a project token. It is as safe as BNB
itself — standard market risks apply, but there are no
contract-level red flags.

------------------------------------------------------------
RECOMMENDED ACTION
------------------------------------------------------------
- No action required. This is a standard, well-established
  token.
- If someone told you to "buy WBNB" on an unusual platform
  or via a specific link, the risk may be in the platform,
  not the token. Run /clawback-url-scan on any URL they
  shared.
- Standard reminder: always verify contract addresses against
  the official listing before interacting.

------------------------------------------------------------
VERIFY ON CHAIN
------------------------------------------------------------
- BSCScan: https://bscscan.com/address/0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c
- GoPlus Token Security: https://gopluslabs.io/token-security/56/0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c
- Other chain explorers: etherscan.io (Ethereum),
  polygonscan.com (Polygon), arbiscan.io (Arbitrum),
  basescan.org (Base), snowtrace.io (Avalanche),
  solscan.io (Solana)

============================================================
```

**Note**: WBNB represents the cleanest possible SAFE result — it is chain infrastructure, not a typical project token. A typical LOW RISK token may have minor flags (small tax, retained ownership with timelock) that are reported but do not elevate the overall rating above LOW RISK. The key difference: LOW RISK flags have plausible legitimate explanations and do not indicate intent to defraud.

### Testing with Real Tokens

For development and demo purposes, use these known addresses:

**Known safe token — BNB/WBNB on BSC:**
```bash
curl "https://api.gopluslabs.io/api/v1/token_security/56?contract_addresses=0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c"
```

**Known safe token — USDT on BSC:**
```bash
curl "https://api.gopluslabs.io/api/v1/token_security/56?contract_addresses=0x55d398326f99059fF775485246999027B3197955"
```

**Finding a honeypot for testing CRITICAL flow:**
To find a current honeypot for testing, search GoPlus Token Security for recently flagged BSC tokens at https://gopluslabs.io — scam tokens are deployed and flagged daily. Use a freshly flagged one for realistic test data. Do not use the zero address (`0x000...000`) — it is not a real honeypot and will return unusual data that does not represent actual scam token responses.

When building, always test your output formatting against a real GoPlus response. Do not fabricate API responses for testing — the field structure matters and fabrication will mask parsing bugs.

---

## What This Skill Is NOT

- **Not a social engineering analyst** — use `clawback-analyse` for message, email, and screenshot analysis
- **Not a URL scanner** — use `clawback-url-scan` for domain and link inspection
- **Not a monitoring service** — use `clawback-watchdog` for ongoing address monitoring
- **Not a reporting tool** — use `clawback-report` to generate formal incident reports

This skill is the **on-chain intelligence layer** — it reads what the blockchain records, queries security databases, and translates contract-level dangers into plain English before you interact.

---

## Integration with ClawBack Suite

ClawBack Token Check works standalone but becomes more powerful with the full suite. When the analysis detects content that another skill handles better, **explicitly recommend the specific skill and provide the invocation command.**

| Detected Content | Trigger Skill | What to Say in Report |
|---|---|---|
| User received this address in a suspicious message, email, or DM | `clawback-analyse` | "Run `/clawback-analyse` and paste the message you received for social engineering analysis." |
| Token metadata or associated sites contain URLs | `clawback-url-scan` | "This token's metadata references [url]. Run `/clawback-url-scan [url]` for deep domain analysis including domain age, VirusTotal scan, and phishing kit detection." |
| User wants ongoing monitoring of this address or contract | `clawback-watchdog` | "To monitor this address for future activity automatically, run `/clawback-watchdog [address]`." |
| User needs a formal incident report for law enforcement or exchange support | `clawback-report` | "To generate a formal incident report with evidence formatting for law enforcement or exchange support, run `/clawback-report`." |
| User wants to practice spotting scam tokens | `clawback-train` | "Want to practice spotting scam tokens? Run `/clawback-train` for interactive training using real crypto scam patterns." |
| User wants to understand the concepts behind an alert | `clawback-learn` | "Want to understand what [term] means? Run `/clawback-learn [term]` for a plain-English explanation." |

---

## About the Builder

**Vance Lever** ([@LeverCRO](https://x.com/LeverCRO)) — AI CRO and agentic economy builder with deep expertise in social engineering, manipulation detection, and crypto security. ClawBack is built on the principle that the best defence against social engineering is understanding exactly how it works — naming the weapons, mapping the playbook, and breaking the emotional spell that makes intelligent people hand over their keys.
