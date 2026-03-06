---
name: clawback-url-scan
description: URL, domain, and contact verification scanner for crypto phishing detection. Use when a user pastes a URL, domain, email address, phone number, or Telegram handle and wants to know if it is safe. Checks against a known-safe domain list for major crypto platforms, then runs deep analysis via VirusTotal and URLScan.io. Detects phishing domains, homoglyph spoofing, visual clone sites, malicious redirects, and brand impersonation. Returns a clear PHISHING / SUSPICIOUS / LIKELY SAFE verdict with domain age, SSL analysis, security engine results, and recommended actions. Activates automatically when a user pastes a URL or asks "is this link safe", or explicitly via /clawback-url-scan.
metadata: {"clawdbot":{"emoji":"🔗","homepage":"https://clawback.bot"},"openclaw":{"primaryEnv":"VIRUSTOTAL_API_KEY"}}
---

# ClawBack URL Scan

**URL, domain, and contact verification scanner for crypto phishing detection.**

Built by Vance Lever ([@LeverCRO](https://x.com/LeverCRO)) — AI CRO, agentic economy builder, crypto security researcher. Part of the [ClawBack](https://github.com/LeverCRO/clawback) skill suite for the Binance OpenClaw AI Innovation Competition.

---

## When This Skill Activates

This skill activates in two ways:

1. **Automatically** — when a user pastes a URL, domain, or email address and asks whether it is safe
2. **Explicitly** — when a user invokes `/clawback-url-scan`

### Supported Input Types

- Full URLs (`https://binance-secure-verify.com/login`)
- Bare domains (`binance-secure-verify.com`)
- Email addresses (`support@binance-verify.com`) — the domain is extracted and checked
- Phone numbers — checked against known platform contact patterns only (no domain to scan)
- Telegram / Discord usernames — checked against known platform contact patterns only
- URLs extracted from messages analysed by `clawback-analyse`

If a user pastes a link, domain, or contact — this skill can check it.

---

## Input Parsing

Before making any API call, parse and normalise the input:

```
INPUT: raw string from user

STEP 1 — Classify the input
├── Starts with http:// or https:// → Full URL
│   → Extract domain from URL
│   → Preserve full URL for submission to VirusTotal + URLScan
│
├── Contains @ → Email address
│   → Extract domain after @
│   → Run domain checks only (no URL submission)
│   → Also check full address against known legitimate senders
│
├── Starts with + or is all digits (7-15 chars) → Phone number
│   → Known domain matching only (no domain to scan)
│   → Note: "Major exchanges do not initiate contact via phone.
│     Any unsolicited call claiming to be exchange support is
│     fraudulent regardless of the number."
│
├── Starts with @ or matches username pattern → Social handle
│   → Known domain matching only (no domain to scan)
│   → Note: "Verify any social account claiming to represent an
│     exchange through the exchange's official website, not by
│     trusting the account itself."
│
├── Contains a dot and no spaces → Bare domain
│   → Prepend https:// for URL submission
│   → Run full domain + URL checks
│
├── Starts with javascript: or data: → CRITICAL
│   → Do NOT visit or submit. Flag immediately:
│     "This is not a URL — it is executable code disguised as a link.
│      javascript: and data: URIs can execute arbitrary code in your
│      browser. DO NOT click this under any circumstances."
│   → Verdict: PHISHING, Confidence: 100%
│
└── Anything else → INVALID
    → Tell user the input format was not recognised
    → Ask them to paste the full URL or domain

STEP 2 — Normalise the domain
├── URL-decode any percent-encoded characters (%2F, %2E, etc.)
│   → Scammers use encoding to hide the real domain
├── Extract the registered domain (effective TLD + 1)
│   → binance.evil.com → registered domain is evil.com
│   → evil.binance.com → registered domain is binance.com
├── Check for IP-based URLs (http://192.168.1.1/...)
│   → Flag: "This URL uses an IP address instead of a domain name.
│     Legitimate exchanges always use named domains. This is
│     suspicious by default."
└── Check for URL shorteners (bit.ly, t.co, tinyurl.com, etc.)
    → Note: "This is a shortened URL. The real destination is hidden.
      Submitting to URLScan will reveal the final redirect target."

STEP 3 — Run checks in sequence
├── Known domain matching (always first — instant, no API needed)
├── VirusTotal domain/URL report (if API key available)
├── URLScan.io submission + poll (if API key available)
└── Compile results into single report
```

---

## Setup

This skill works at three levels. Each level adds detection confidence, but the skill provides value at every level.

**Level 1 — Zero config (always available):**
Known domain matching + LLM heuristics (homoglyphs, subdomain tricks, brand impersonation). No setup required.

**Level 2 — Add VirusTotal (recommended):**
Register free at virustotal.com and get an API key. Enables domain age, security engine scanning, SSL analysis, and registrar checks.

**Level 3 — Add URLScan.io (full capability):**
Register free at urlscan.io and get an API key. Enables visual clone detection, phishing kit analysis, redirect chain tracking, and page screenshots.

### Configuring API Keys in OpenClaw

Add your keys to `~/.openclaw/openclaw.json` under the skill entry:

```json
{
  "skills": {
    "entries": {
      "clawback-url-scan": {
        "enabled": true,
        "env": {
          "VIRUSTOTAL_API_KEY": "your-virustotal-key",
          "URLSCAN_API_KEY": "your-urlscan-key"
        }
      }
    }
  }
}
```

Or via CLI:

```
openclaw config set skills.entries.clawback-url-scan.env.VIRUSTOTAL_API_KEY "your-key"
openclaw config set skills.entries.clawback-url-scan.env.URLSCAN_API_KEY "your-key"
```

Both keys are free. Level 2 is strongly recommended — without VirusTotal, the skill cannot check domain age or security engine verdicts.

**How API Keys Are Accessed at Runtime:**

OpenClaw injects `skills.entries.<skill>.env` variables into the process environment at runtime. All API calls use curl with shell variable expansion (`$VARIABLE_NAME`) piped into Python for JSON parsing. The shell expands environment variables before the agent processes the command — this avoids security refusals that occur when agents access `os.environ` directly.

Users configure keys via the Skills page in the Control UI or via:

```
openclaw config set skills.entries.clawback-url-scan.env.VIRUSTOTAL_API_KEY your-key
openclaw config set skills.entries.clawback-url-scan.env.URLSCAN_API_KEY your-key
```

If a key is not configured, the curl call will fail with a 401/403 error which the Python parser catches and reports with setup instructions.

---

## API Stack

### 1. Known Domain Matching (Instant Check)

Before calling any external API, the skill checks the input domain against a list of known legitimate domains for major crypto platforms. **This is a local pattern match — not a live query to any verification service.**

**Known legitimate crypto platform domains:**

| Platform | Known Legitimate Domains |
|---|---|
| **Binance** | `binance.com`, `binance.cloud`, `post.binance.com`, `academy.binance.com`, `research.binance.com`, `launchpad.binance.com`, `p2p.binance.com`, `binance.zendesk.com` |
| **Coinbase** | `coinbase.com`, `pro.coinbase.com` |
| **MetaMask** | `metamask.io` |
| **Uniswap** | `app.uniswap.org` |
| **PancakeSwap** | `pancakeswap.finance` |
| **Trust Wallet** | `trustwallet.com` |

**Known legitimate Binance email senders:**

| Email Address | Usage |
|---|---|
| `do-not-reply@post.binance.com` | Transaction notifications |
| `no-reply@post.binance.com` | Account notifications |
| `do-not-reply@notice.binance.com` | Marketing and announcements |

> **Note**: This list is not exhaustive and may not reflect domains added after March 2026. A domain NOT being in this list does not mean it is malicious — it means it was not in ClawBack's known-safe list at build time. Always recommend users verify through the platform's official channels for definitive confirmation.
>
> **Staleness rule**: When a domain is not in this list but could plausibly be legitimate (e.g. a new subdomain of binance.com), do NOT declare it phishing based solely on absence. Instead report: "This domain is not in ClawBack's known domains list. Verify it directly through the platform's official website." Only flag as phishing if other signals (domain age, engine detections, structural analysis) support that verdict.

**Decision logic:**

```
IF domain exactly matches or is a subdomain of a known legitimate domain
  → Report: "✅ This matches a known legitimate [platform] domain."
  → Still run VirusTotal/URLScan (domain could be compromised, or the
    user may have a typo the skill didn't catch)
  → Skip to LIKELY SAFE unless API checks reveal something unexpected

IF domain contains a platform name (binance, coinbase, metamask, etc.)
  but does NOT match a known legitimate domain
  → Report: "🚨 This domain contains '[platform name]' but is NOT a
    known official domain for [platform]. This is a common phishing pattern."
  → Elevate to SUSPICIOUS minimum, proceed to full API checks

IF domain doesn't reference any known platform
  → Skip this check entirely, proceed directly to API checks
  → Do NOT include a PLATFORM CHECK section in the output
```

### 2. VirusTotal (Primary URL/Domain Scanner)

**Free tier**: 4 lookups/min, 500/day. Requires API key (free registration at [virustotal.com](https://www.virustotal.com)).

**Endpoints:**

| Endpoint | Method | Purpose |
|---|---|---|
| `https://www.virustotal.com/api/v3/urls` | POST | Submit a URL for analysis. Body: `url={url-encoded-string}`. Returns URL ID for lookup. |
| `https://www.virustotal.com/api/v3/urls/{url_id}` | GET | Retrieve URL analysis results. |
| `https://www.virustotal.com/api/v3/domains/{domain}` | GET | Retrieve domain report (age, registrar, SSL, engine results). |

**Authentication**: All requests require header `x-apikey: {VIRUSTOTAL_API_KEY}`

**Submitting a URL:**
```bash
# Submit URL for analysis
curl -X POST "https://www.virustotal.com/api/v3/urls" \
  -H "x-apikey: $VIRUSTOTAL_API_KEY" \
  --form "url=https://binance-secure-verify.com"

# Response includes id — use it to fetch results
```

**Domain lookup (preferred — curl + Python):**
```bash
# VirusTotal domain lookup (Level 2)
curl -s "https://www.virustotal.com/api/v3/domains/DOMAIN_HERE" \
  -H "x-apikey: $VIRUSTOTAL_API_KEY" | python3 -c "
import json, sys
try:
    d = json.load(sys.stdin)
    if 'error' in d:
        print(json.dumps({'error': d['error'].get('code','unknown'), 'hint': 'Check VIRUSTOTAL_API_KEY is configured. Add via Skills page in Control UI or: openclaw config set skills.entries.clawback-url-scan.env.VIRUSTOTAL_API_KEY your-key'}))
        sys.exit(0)
    a = d['data']['attributes']
    out = {
        'creation_date': a.get('creation_date'),
        'registrar': a.get('registrar'),
        'reputation': a.get('reputation'),
        'last_analysis_stats': a.get('last_analysis_stats'),
        'categories': a.get('categories'),
        'popularity_ranks': a.get('popularity_ranks'),
        'last_https_certificate': {
            'issuer': a.get('last_https_certificate',{}).get('issuer'),
            'validity': a.get('last_https_certificate',{}).get('validity'),
        } if a.get('last_https_certificate') else None,
        'whois': a.get('whois','')[:500]
    }
    flagged = {k:v for k,v in a.get('last_analysis_results',{}).items()
               if v.get('category') in ('malicious','phishing','suspicious')}
    out['flagged_engines'] = flagged
    print(json.dumps(out, indent=2, default=str))
except Exception as e:
    print(json.dumps({'error': str(e)}))
"
```

**URLScan submission (curl + Python):**
```bash
# URLScan.io submission (Level 3)
curl -s -X POST "https://urlscan.io/api/v1/scan/" \
  -H "API-Key: $URLSCAN_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"url": "https://DOMAIN_HERE", "visibility": "public"}' | python3 -c "
import json, sys
try:
    d = json.load(sys.stdin)
    if 'message' in d and 'error' in d.get('message','').lower():
        print(json.dumps({'error': d['message'], 'hint': 'Check URLSCAN_API_KEY. Add via Skills page in Control UI.'}))
    else:
        print(json.dumps({'uuid': d.get('uuid'), 'status': 'submitted', 'result_url': d.get('result','')}, indent=2))
except Exception as e:
    print(json.dumps({'error': str(e)}))
"
```

**Context Window Management:**

VirusTotal domain responses can include extensive DNS records, WHOIS data, and historical analysis that floods context. ALWAYS use the curl+Python pattern above to extract only the fields documented in the Response Field Mapping table below. It extracts creation_date, registrar, analysis stats, categories, certificate info, truncated WHOIS, and only the engines that flagged the domain — keeping the response compact and parseable.

#### Response Field Mapping

**Domain report** — `GET /api/v3/domains/{domain}`

Response structure: `data.attributes.{field}`

| VirusTotal Field | ClawBack Check | What to Report | Risk Signal |
|---|---|---|---|
| `creation_date` | Domain Age | Unix timestamp → calculate age. Report as "X days/months/years old (created YYYY-MM-DD)" | < 30 days = HIGH RISK. < 90 days = MEDIUM. > 1 year = lower risk. |
| `registrar` | Registrar Analysis | Name of the domain registrar | Some registrars are heavily used for fraud (Namesilo, Porkbun for throwaway domains). Not conclusive alone. |
| `last_analysis_stats` | Engine Detection | Object: `{malicious, suspicious, harmless, undetected, timeout}` — integer counts | `malicious > 0` = HIGH RISK. `suspicious > 0` = MEDIUM. All zero + `harmless > 0` = good sign. |
| `last_analysis_results` | Per-Engine Verdicts | Object keyed by engine name, each with `{category, result, method, engine_name}` | List engines that returned `category: "malicious"` or `"phishing"` by name. |
| `last_https_certificate` | SSL Certificate | Object: `{issuer, validity, subject}` | Free certs (Let's Encrypt, ZeroSSL) on brand-impersonating domains = red flag. Not a flag on normal sites. |
| `last_https_certificate.issuer` | SSL Issuer | String like `"C=US, O=Let's Encrypt, CN=R3"` | Report issuer name. Flag if free cert + brand impersonation. |
| `last_https_certificate.validity.not_after` | SSL Expiry | ISO date string | Very short-lived certs (< 90 days) on phishing sites are common. |
| `last_dns_records` | DNS Records | Array of DNS records (A, AAAA, MX, etc.) | IP addresses, hosting provider. Informational. |
| `whois` | WHOIS Data | Raw WHOIS text (if available) | Check for privacy-protected registration on brand-impersonating domains. |
| `popularity_ranks` | Site Popularity | Object with ranking data from Alexa, Majestic, etc. | Legitimate exchange domains have high rankings. Unknown phishing domains have none. |
| `categories` | Site Category | Object with per-vendor categorisations | Already categorised as "phishing" by vendors = strong signal. |

**URL report** — `GET /api/v3/urls/{url_id}`

Same `last_analysis_stats` and `last_analysis_results` structure as domain report, but specific to the submitted URL (catches path-specific phishing that domain-level checks might miss).

Additional fields:

| VirusTotal Field | ClawBack Check | What to Report |
|---|---|---|
| `final_url` | Redirect Detection | If different from submitted URL, a redirect occurred. Report both. |
| `url` | Submitted URL | The URL as submitted. |
| `last_http_response_content_length` | Page Size | Very small pages may be redirect stubs. Very large may contain obfuscated phishing kits. |
| `last_http_response_code` | HTTP Status | 200 = live. 404 = dead but domain exists. 30x = redirect. |

### 3. URLScan.io (Visual Scanner + Phishing Kit Detection)

**Free tier**: 100 scans/day, 2 scans/min. Requires API key (free registration at [urlscan.io](https://urlscan.io)).

URLScan is particularly valuable for **phishing kit detection** — it renders the page, takes a screenshot, and analyses the DOM for brand-specific elements (logos, login forms, CSS) that indicate a cloned site.

**Endpoints:**

| Endpoint | Method | Purpose |
|---|---|---|
| `https://urlscan.io/api/v1/scan/` | POST | Submit URL for scanning. Returns UUID. |
| `https://urlscan.io/api/v1/result/{uuid}/` | GET | Retrieve scan results (poll until ready). |

**Authentication**: Header `API-Key: {URLSCAN_API_KEY}`

**Submitting a scan:**
```bash
# Submit
curl -X POST "https://urlscan.io/api/v1/scan/" \
  -H "API-Key: $URLSCAN_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"url": "https://binance-secure-verify.com", "visibility": "public"}'

# Response: {"uuid": "xxx-xxx-xxx", ...}
# Poll results (may take 10-30 seconds):
curl "https://urlscan.io/api/v1/result/xxx-xxx-xxx/" \
  -H "API-Key: $URLSCAN_API_KEY"
```

#### Response Field Mapping

**Scan result** — `GET /api/v1/result/{uuid}/`

| URLScan Field | ClawBack Check | What to Report | Risk Signal |
|---|---|---|---|
| `page.url` | Final URL | The URL after all redirects resolved | Different from submitted URL = redirect detected |
| `page.domain` | Final Domain | Domain after redirects | Domain changed during redirect = suspicious |
| `page.ip` | Hosting IP | IP address serving the page | Informational — shared hosting is common but notable |
| `page.server` | Server Software | Web server identity | Informational |
| `page.title` | Page Title | HTML `<title>` content | If it says "Binance" on a non-Binance domain = brand impersonation |
| `verdicts.overall.malicious` | URLScan Verdict | Boolean — URLScan's own malicious assessment | `true` = HIGH RISK |
| `verdicts.overall.score` | Threat Score | 0-100 score | > 50 = suspicious. > 75 = likely malicious. |
| `verdicts.engines.verdicts` | Engine Verdicts | Array of per-engine assessments | List any engines flagging as malicious |
| `lists.urls` | Linked URLs | All URLs loaded by the page | Check for data exfiltration endpoints, external script loading |
| `lists.domains` | Linked Domains | All domains contacted during page load | Check for suspicious external domains (keyloggers, C2 servers) |
| `stats.tlsPercentage` | TLS Usage | Percentage of resources loaded over HTTPS | Low TLS percentage on a site claiming to be a major exchange = red flag |
| `meta.processors.download` | Downloads | Files the page attempts to download | Any automatic downloads = CRITICAL flag |
| `task.screenshot` | Screenshot URL | URL to rendered screenshot of the page | Use for visual clone detection — does it look like a known exchange? |

> **Note**: URLScan response structure should be validated against a real API call before relying on nested field paths. The field names above are documented from URLScan's published API spec.

### Error & Rate Limit Handling

| Scenario | Action |
|---|---|
| VirusTotal 200 + results | Parse and report |
| VirusTotal 404 (domain not found) | "This domain has no VirusTotal history — it may be too new or too obscure to have been scanned before. Absence of data is itself a signal — legitimate established sites have VirusTotal records." |
| VirusTotal 429 (rate limit) | Wait 15 seconds, retry once. If still 429, continue with URLScan + known domain matching only. |
| VirusTotal 401 (bad API key) | "VirusTotal API key is invalid or not configured. Skipping engine detection. Configure via `openclaw config set skills.entries.clawback-url-scan.env.VIRUSTOTAL_API_KEY`." |
| URLScan 200 + results | Parse and report |
| URLScan result not ready | Poll every 5 seconds, up to 60 seconds. If still pending, note: "URLScan is still processing. Visual clone detection results are not yet available." |
| URLScan 429 (rate limit) | Skip URLScan. Note: "URLScan rate limit reached. Visual clone detection was not performed." |
| URLScan 401 (bad API key) | "URLScan API key is invalid or not configured. Skipping visual analysis. Configure via `openclaw config set skills.entries.clawback-url-scan.env.URLSCAN_API_KEY`." |
| Both APIs unavailable | Fall back to known domain matching + LLM reasoning. Use ❓ for all checks. State: "⚠️ APIS UNAVAILABLE — This analysis is based on known domain matching and LLM reasoning only. VirusTotal and URLScan checks were not performed. Results are preliminary." |
| No API keys configured at all | Run known domain matching + LLM heuristics (age cannot be determined, but homoglyph detection, subdomain analysis, and brand impersonation checks are all LLM-native). Note which checks were skipped and why. |

**Degradation priority**: Known domain matching + LLM heuristics (always available, no API) → VirusTotal (primary scanner) → URLScan (supplementary visual analysis). Each layer adds confidence but the skill still provides value at Level 1.

---

## Detection Framework

### Domain Intelligence Checks

| Check | What It Detects | How | Risk Level |
|---|---|---|---|
| **Known Domain Match** | Does this domain match a known legitimate crypto platform? | Local pattern match against known-safe domain list. | Matched = strong positive signal. Contains platform name but no match = HIGH (brand impersonation). |
| **Domain Age** | How old is this domain? | VirusTotal `creation_date`. Calculate days since creation. | < 7 days = CRITICAL. < 30 days = HIGH. < 90 days = MEDIUM. > 1 year = informational. |
| **Registrar Analysis** | Where was the domain registered? | VirusTotal `registrar`. Cross-reference with registrars commonly used for throwaway phishing domains. | Informational alone, but amplifies other signals. |
| **SSL Certificate** | Who issued the cert? Is it appropriate for the claimed identity? | VirusTotal `last_https_certificate`. Check issuer, validity period, subject. | Free cert (Let's Encrypt) on a brand-impersonating domain = MEDIUM flag. Not a flag on normal sites — most legitimate sites also use Let's Encrypt. |
| **Homoglyph Detection** | Does the domain use Cyrillic/Greek lookalike characters? | Character-by-character analysis against known substitution table. | Any homoglyph substitution = CRITICAL. This is always intentional deception. |
| **Subdomain Tricks** | Is the domain structured to deceive? | Parse registered domain vs subdomains. `binance.evil.com` → registered domain is `evil.com`, "binance" is just a subdomain. | Deceptive subdomain structure = HIGH. |
| **URL Encoding Tricks** | Are percent-encoded characters hiding the real domain? | Decode all `%XX` sequences before analysis. | Encoded characters in the domain portion = HIGH. |

### Security Engine Checks

| Check | What It Detects | How | Risk Level |
|---|---|---|---|
| **VirusTotal Engine Detection** | How many security engines flag this URL/domain? | `last_analysis_stats.malicious` + `last_analysis_stats.suspicious` counts. | Any malicious > 0 = HIGH. Suspicious only = MEDIUM. All clean = good sign. |
| **VirusTotal Category** | Has the domain been categorised as phishing by vendors? | `categories` field — check for "phishing", "malware", "scam" labels. | Categorised as phishing = CRITICAL. |
| **URLScan Verdict** | Does URLScan's own engine flag it? | `verdicts.overall.malicious` boolean + `verdicts.overall.score`. | Score > 75 = HIGH. Score > 50 = MEDIUM. |
| **URLScan Engine Array** | Do URLScan's integrated engines flag it? | `verdicts.engines.verdicts` array. | Any engine flagging = HIGH. |

### Behavioural Checks

| Check | What It Detects | How | Risk Level |
|---|---|---|---|
| **Redirect Chain** | Does the URL redirect to a different domain? | Compare submitted URL to URLScan `page.url`. VirusTotal `final_url`. | Redirect to different domain = MEDIUM. Redirect to login page on different domain = HIGH. |
| **Visual Clone Detection** | Does the page visually mimic a known exchange? | URLScan screenshot + page title + DOM analysis for brand-specific elements. | Page title contains exchange name on non-exchange domain = HIGH. Visual similarity to exchange login = CRITICAL. |
| **Automatic Downloads** | Does the page trigger file downloads? | URLScan `meta.processors.download`. | Any automatic download = CRITICAL. |
| **External Script Loading** | Does the page load scripts from suspicious domains? | URLScan `lists.domains` — check for keylogger or exfiltration endpoints. | Known malicious external domains = CRITICAL. |

### Homoglyph Reference Table

| Real Character | Fake (Cyrillic/Greek) | Looks Like | Commonly Spoofed In |
|---|---|---|---|
| `a` (Latin) | `а` (Cyrillic U+0430) | Identical | bin**a**nce |
| `e` (Latin) | `е` (Cyrillic U+0435) | Identical | binanc**e** |
| `o` (Latin) | `о` (Cyrillic U+043E) | Identical | c**o**inbase |
| `p` (Latin) | `р` (Cyrillic U+0440) | Identical | su**pp**ort |
| `c` (Latin) | `с` (Cyrillic U+0441) | Identical | binan**c**e |
| `i` (Latin) | `і` (Cyrillic U+0456) | Identical | b**i**nance |
| `n` (Latin) | `ո` (Armenian U+0578) | Similar | bi**n**ance |

If any character in the domain has a Unicode code point outside the basic Latin range (U+0000–U+007F) and visually resembles a Latin character, it is a homoglyph substitution. This is **always** intentional deception — verdict: PHISHING.

### Conflicting Results Between APIs

When VirusTotal and URLScan produce conflicting assessments — for example, VirusTotal shows 0 engine detections but URLScan scores the page as malicious, or vice versa — apply these rules:

1. **The more dangerous assessment sets the floor of the verdict**, same as the priority rule for risk scoring. Conflicting results increase suspicion — they do not cancel out.
2. **Note the disagreement explicitly in the report**: "VirusTotal shows no engine detections, but URLScan flagged this page as malicious based on [reason]. The conflicting results may indicate a very new phishing site that VirusTotal engines have not indexed yet."
3. **If VirusTotal flags it but URLScan is clean**, the VirusTotal engine detections take priority — URLScan may not have detected the phishing kit in the DOM.
4. **Never average or split the difference.** Two conflicting signals are more suspicious than one clean result, not less.

---

## Risk Scoring

| Rating | Criteria | Action |
|---|---|---|
| **PHISHING** | VirusTotal engines flag as malicious, domain < 7 days old, homoglyph detected, visual clone confirmed, `javascript:`/`data:` URI, or known phishing categorisation | Do not visit. Do not enter any information. If you already visited, assume credentials are compromised. |
| **SUSPICIOUS** | New domain (< 90 days) with no detections yet, unusual registrar, free SSL on brand-impersonating domain, redirect to unexpected domain, or minor flags without conclusive evidence | Do not interact until verified through official channels. Wait for more scanning data. |
| **LIKELY SAFE** | No engine detections, established domain age (> 1 year), valid SSL from commercial CA, no brand impersonation, no redirects, or matched against known-safe domain list | No red flags found. Standard internet caution still applies. |

**Priority rule**: The most dangerous flag determines the floor of the rating. A domain that passes every check except homoglyph detection is **PHISHING**, not "mostly safe with one concern."

**Important nuance on new domains with no detections**: A 3-day-old domain with zero VirusTotal detections is **not** LIKELY SAFE — it means security engines haven't scanned it yet. New + no detections = SUSPICIOUS at minimum. New + brand impersonation + no detections = PHISHING (engines haven't caught up yet, but the intent is clear).

---

## Output Format

Every analysis produces a structured report. The most dangerous finding always leads.

```
============================================================
CLAWBACK URL ANALYSIS
============================================================

URL: [full URL checked]
DOMAIN: [extracted registered domain]
VERDICT: [PHISHING / SUSPICIOUS / LIKELY SAFE]

------------------------------------------------------------
PLATFORM CHECK
------------------------------------------------------------
[Only include this section when the domain references a known
 platform name (binance, coinbase, metamask, etc.).
 If the domain doesn't reference any known platform, omit
 this section entirely — go straight to CRITICAL FLAGS or
 DOMAIN INTELLIGENCE.]

✅ / 🚨 [Result of known domain matching]

------------------------------------------------------------
CRITICAL FLAGS
------------------------------------------------------------
🚨 [Flag name]: [Plain English explanation]

[Only present if PHISHING-level flags exist.
 If no critical flags: "No critical flags detected."]

------------------------------------------------------------
DOMAIN INTELLIGENCE
------------------------------------------------------------
✅ / ⚠️ / 🚨 Domain Age: [age + creation date]
✅ / ⚠️ / 🚨 Registrar: [registrar name + assessment]
✅ / ⚠️ / 🚨 SSL Certificate: [issuer, expiry, assessment]
✅ / ⚠️ / 🚨 Homoglyph Check: [clean or substitutions detected]
✅ / ⚠️ / 🚨 Subdomain Analysis: [legitimate structure or deceptive]

------------------------------------------------------------
SECURITY ENGINE RESULTS
------------------------------------------------------------
VirusTotal: [X/Y engines flagged as malicious, Z as suspicious]
[List specific engines that flagged it, if any]

URLScan.io: [Verdict + score. Phishing kit detected? Redirects?
Screenshot available at: [urlscan screenshot URL]]

------------------------------------------------------------
WHAT THIS MEANS FOR YOU
------------------------------------------------------------
[2-3 sentence plain English summary. Lead with the most
important finding.]

------------------------------------------------------------
IF YOU ALREADY VISITED THIS SITE
------------------------------------------------------------
[Include for PHISHING and SUSPICIOUS verdicts only.
 Omit entirely for LIKELY SAFE.]

- If you visited but did NOT enter any information: Clear your
  browser cache and cookies for this domain. Monitor your
  accounts for unusual activity. You are likely safe.
- If you entered credentials (email, password, 2FA): Change
  your password NOW on the real platform. Reset 2FA. Check
  for unauthorised withdrawals or address changes.
- If you connected a crypto wallet: Check for new token
  approvals immediately. Revoke any you did not authorise.
  Run /clawback-token-check [your-wallet-address] to scan
  for risky approvals.
- If you downloaded anything: Do not open the file. Run a
  malware scan. If you already opened it, assume your device
  may be compromised — change passwords from a different device.

------------------------------------------------------------
RECOMMENDED ACTION
------------------------------------------------------------
- [Specific steps: do not visit, revoke credentials if
  already entered, report the domain, etc.]
- [For Binance-related phishing/suspicious domains, include:
  "Verify this domain at https://www.binance.com/en/official-verification"]
- [For other platform domains, include the platform's official
  verification channel if known]
- [If user arrived here from clawback-analyse: connect the
  findings back to the original message analysis]

============================================================
```

### Section Rules by Verdict Type

| Verdict | Sections | Modifications |
|---|---|---|
| **PHISHING** | All sections as shown, including IF YOU ALREADY VISITED THIS SITE. CRITICAL FLAGS required. PLATFORM CHECK included if domain references a known platform. | Lead with the most dangerous finding. If visual clone detected, reference the URLScan screenshot. For Binance-related domains, include Binance Verify link in RECOMMENDED ACTION. |
| **SUSPICIOUS** | All sections, including IF YOU ALREADY VISITED THIS SITE. CRITICAL FLAGS replaced with "No critical flags confirmed — but see warnings below." PLATFORM CHECK included if domain references a known platform. | Explain what is inconclusive and what would change the verdict. |
| **LIKELY SAFE** | PLATFORM CHECK (if applicable), DOMAIN INTELLIGENCE, SECURITY ENGINE RESULTS, WHAT THIS MEANS FOR YOU, RECOMMENDED ACTION. | Drop CRITICAL FLAGS section. Omit IF YOU ALREADY VISITED THIS SITE. RECOMMENDED ACTION still includes standard caution ("verify independently, don't rely solely on this scan"). If matched against known-safe list, lead with that. |

### Status Icons

| Icon | Meaning |
|---|---|
| ✅ | Check passed — no risk detected |
| ⚠️ | Warning — elevated risk, review recommended |
| 🚨 | Critical — confirmed danger or high-severity flag |
| ❓ | Unknown — API unavailable, check could not be performed |

---

## Skill Behaviour

### Tone and Posture

Same as all ClawBack skills — security professional, not customer service.

- **Lead with danger**: If a homoglyph is detected, say "This domain uses fake characters to impersonate [platform]" before any other detail.
- **Plain English first**: "3 out of 90 security engines flagged this as phishing" is more useful than raw JSON.

**CRITICAL: You MUST actually execute the API call.** Do not simulate, estimate, or fabricate API responses. If the Python or curl command fails, report the error honestly. The user is relying on real on-chain data, not your estimation of what the data might look like. A wrong SAFE verdict based on fabricated data could cost the user their funds.
- **Direct**: A 2-day-old domain impersonating Binance with a free SSL cert is PHISHING, not "potentially suspicious pending further analysis."
- **Protective**: If the user already visited the URL or entered credentials, lead with damage control steps.

### Input Handling Edge Cases

- **URL shorteners** (bit.ly, t.co, tinyurl.com, goo.gl, etc.): Submit the shortened URL to URLScan — it will follow redirects and reveal the final destination. Report both the shortener URL and the final URL. Note: "This is a shortened URL. The real destination was hidden behind the shortener."
- **URL-encoded characters**: Decode all `%XX` sequences before domain analysis. Report both the encoded and decoded versions. Flag if encoding was used to obscure the domain portion (not just query parameters — that's normal).
- **`javascript:` and `data:` URIs**: These are not URLs — they execute code in the browser. Flag immediately as PHISHING with 100% confidence. Do not submit to any API. Warn: "This is executable code disguised as a link, not a website address."
- **IP-based URLs** (`http://192.168.1.1/`, `http://45.33.x.x/`): No domain to check. Flag as SUSPICIOUS by default — legitimate exchanges never use IP-based URLs. Still submit to VirusTotal/URLScan if the IP is public.
- **Already-dead domains**: If the site is down (URLScan can't render, VirusTotal shows no recent HTTP response), still report domain age and engine history. Note: "This site appears to be offline. This is common for phishing sites — they are often taken down after a campaign. The domain history still shows [findings]."
- **Internationalized domain names (IDN)**: Domains using Punycode (`xn--`) should be decoded to their Unicode representation for homoglyph analysis. Report both the Punycode and displayed form.

### Handling Multiple URLs

If a user pastes multiple URLs, check each one separately. If they appear related (same registrar, similar domain pattern, created on the same date), note the connection — this may indicate a phishing campaign using multiple domains.

---

## Example Analyses

### Example 1: Obvious Phishing Domain

**User input**: "Is this safe? https://binance-secure-verify.com/account/login"

```
============================================================
CLAWBACK URL ANALYSIS
============================================================

URL: https://binance-secure-verify.com/account/login
DOMAIN: binance-secure-verify.com
VERDICT: 🚨 PHISHING

------------------------------------------------------------
PLATFORM CHECK
------------------------------------------------------------
🚨 NOT A KNOWN BINANCE DOMAIN.
"binance-secure-verify.com" contains "binance" but does NOT
match any known official Binance domain. The name is designed
to look official by combining "binance" + "secure" + "verify"
— this is a textbook phishing domain naming pattern.

Known Binance domains: binance.com, binance.cloud

------------------------------------------------------------
CRITICAL FLAGS
------------------------------------------------------------
🚨 PHISHING DOMAIN: 7 out of 90 VirusTotal engines flag this
   domain as malicious/phishing. Combined with the brand
   impersonation and 4-day domain age, this is a confirmed
   phishing site.

🚨 BRAND IMPERSONATION: Domain contains "binance" but is not
   an official Binance domain. The URL path "/account/login"
   mimics the Binance login page structure.

------------------------------------------------------------
DOMAIN INTELLIGENCE
------------------------------------------------------------
🚨 Domain Age: 4 days old (created 2026-03-01)
   Registered less than a week ago. Legitimate exchange
   domains are years old. Phishing domains are days old.

⚠️ Registrar: Namesilo LLC
   Namesilo is a legitimate registrar but is commonly used
   for disposable phishing domains due to low prices and
   minimal verification.

⚠️ SSL Certificate: Let's Encrypt (expires in 89 days)
   Free SSL certificate. Normal for most websites, but on a
   domain impersonating a major exchange, it indicates the
   operator invested nothing in legitimacy. Real Binance uses
   commercial Extended Validation certificates.

✅ Homoglyph Check: No character substitutions detected.
   Domain uses standard Latin characters — the deception is
   in the domain name structure, not character spoofing.

🚨 Subdomain Analysis: DECEPTIVE STRUCTURE
   "binance-secure-verify" is the registered domain, not a
   Binance subdomain. Compare:
   - verify.binance.com → subdomain of binance.com (legitimate)
   - binance-secure-verify.com → entirely separate domain (phishing)

------------------------------------------------------------
SECURITY ENGINE RESULTS
------------------------------------------------------------
VirusTotal: 7/90 engines flagged as malicious
  - ESET: Phishing
  - Fortinet: Phishing
  - Google Safebrowsing: Social engineering
  - Kaspersky: Phishing
  - Sophos: Malware
  - BitDefender: Phishing
  - Netcraft: Malicious

URLScan.io: Score 87/100 (Malicious)
  - Page renders a Binance login form clone
  - Page title: "Login | Binance"
  - Form action posts to binance-secure-verify.com/api/auth
    (NOT to real Binance)
  - Screenshot: [urlscan screenshot link]

------------------------------------------------------------
WHAT THIS MEANS FOR YOU
------------------------------------------------------------
This is a confirmed phishing site impersonating Binance. It
was created 4 days ago, mimics the Binance login page, and
is already flagged by 7 security engines. If you enter your
credentials here, the attacker will capture your email,
password, and 2FA code in real time and use them to access
your real Binance account.

------------------------------------------------------------
IF YOU ALREADY VISITED THIS SITE
------------------------------------------------------------
- If you visited but did NOT enter any information: Clear your
  browser cache and cookies for this domain. Monitor your
  Binance account for unusual activity. You are likely safe.
- If you entered credentials (email, password, 2FA):
  1. Change your Binance password NOW from the official app
  2. Reset your 2FA immediately
  3. Check withdrawal addresses — remove any you don't recognise
  4. Freeze withdrawals via the Binance app
  5. Contact Binance support via in-app chat only
- If you connected a crypto wallet: Check for new token
  approvals immediately. Revoke any you did not authorise.
  Run /clawback-token-check [your-wallet-address] to scan
  for risky approvals.
- If you downloaded anything: Do not open the file. Run a
  malware scan. If you already opened it, assume your device
  may be compromised — change passwords from a different device.

------------------------------------------------------------
RECOMMENDED ACTION
------------------------------------------------------------
- DO NOT visit this URL. Do not enter any information.
- Verify this domain is not legitimate at:
  https://www.binance.com/en/official-verification
- If someone sent you this link, run /clawback-analyse on the
  message for social engineering analysis.
- Report the domain: https://safebrowsing.google.com/safebrowsing/report_phish/

============================================================
```

### Example 2: Suspicious New Domain — Inconclusive

**User input**: "Someone in a Telegram group shared this: https://bnb-stake-rewards.io"

```
============================================================
CLAWBACK URL ANALYSIS
============================================================

URL: https://bnb-stake-rewards.io
DOMAIN: bnb-stake-rewards.io
VERDICT: ⚠️ SUSPICIOUS

------------------------------------------------------------
PLATFORM CHECK
------------------------------------------------------------
🚨 NOT A KNOWN BINANCE DOMAIN.
"bnb-stake-rewards.io" references "BNB" (associated with
Binance) but does not match any known official Binance domain.

------------------------------------------------------------
CRITICAL FLAGS
------------------------------------------------------------
No critical flags confirmed — but see warnings below.

------------------------------------------------------------
DOMAIN INTELLIGENCE
------------------------------------------------------------
🚨 Domain Age: 12 days old (created 2026-02-21)
   Very new domain. Legitimate staking platforms have
   established web presences. A 12-day-old domain offering
   staking rewards is a major red flag.

⚠️ Registrar: Porkbun LLC
   Legitimate registrar, but frequently used for low-cost
   disposable domains in phishing campaigns.

⚠️ SSL Certificate: Let's Encrypt (expires in 87 days)
   Free certificate. Not conclusive alone but consistent
   with a low-investment operation.

✅ Homoglyph Check: No character substitutions detected.

✅ Subdomain Analysis: No deceptive subdomain structure.
   The domain is what it claims to be — just not a Binance
   domain.

------------------------------------------------------------
SECURITY ENGINE RESULTS
------------------------------------------------------------
VirusTotal: 0/90 engines flagged as malicious
  No security engines have flagged this domain yet. This does
  NOT mean it is safe — it means it is too new for most
  engines to have scanned. Phishing domains often have a
  0-detection window in their first days before engines
  catch up.

URLScan.io: Score 35/100 (Low threat, but not clean)
  - Page renders a staking dashboard with BNB branding
  - Wallet connect prompt detected on page load
  - No confirmed phishing kit, but the connect prompt
    appearing immediately is a common drainer pattern
  - Screenshot: [urlscan screenshot link]

------------------------------------------------------------
WHAT THIS MEANS FOR YOU
------------------------------------------------------------
This domain has multiple yellow flags but no confirmed red
ones yet. It is 12 days old, uses BNB branding without being
a Binance domain, and immediately prompts wallet connections.
Zero VirusTotal detections means engines haven't caught up —
not that it's safe. The context (shared in a Telegram group,
new domain, staking rewards claim) matches the pattern of
wallet drainer sites.

------------------------------------------------------------
IF YOU ALREADY VISITED THIS SITE
------------------------------------------------------------
- If you visited but did NOT connect your wallet or enter
  any information: Clear your browser cache and cookies for
  this domain. You are likely safe.
- If you connected your wallet:
  1. Check for any new token approvals you didn't authorise
  2. Revoke suspicious approvals at https://bscscan.com/tokenapprovalchecker
  3. Run /clawback-token-check [your-wallet-address] to scan
     for risky approvals
  4. Consider moving funds to a new wallet if unlimited
     approvals were granted
- If you approved any transactions: Assume the approved
  tokens are at risk. Move remaining funds to a new wallet
  immediately.
- If you downloaded anything: Do not open the file. Run a
  malware scan. If you already opened it, assume your device
  may be compromised — change passwords from a different device.

------------------------------------------------------------
RECOMMENDED ACTION
------------------------------------------------------------
- DO NOT connect your wallet to this site.
- DO NOT approve any transactions if prompted.
- Verify this is not a legitimate Binance staking product at:
  https://www.binance.com/en/official-verification
- Legitimate Binance staking is only available at:
  https://www.binance.com/en/earn
- Run /clawback-analyse on the Telegram message where this
  was shared for social engineering analysis.
- Report the Telegram group if it appears to be promoting scams.

============================================================
```

### Example 3: Legitimate Binance URL

**User input**: "Just want to double-check — is https://www.binance.com/en/trade/BTC_USDT legit?"

```
============================================================
CLAWBACK URL ANALYSIS
============================================================

URL: https://www.binance.com/en/trade/BTC_USDT
DOMAIN: binance.com
VERDICT: ✅ LIKELY SAFE

------------------------------------------------------------
PLATFORM CHECK
------------------------------------------------------------
✅ KNOWN BINANCE DOMAIN.
"binance.com" matches ClawBack's known-safe list for Binance.
This URL points to the BTC/USDT trading pair page, which is
a standard Binance trading interface.

------------------------------------------------------------
DOMAIN INTELLIGENCE
------------------------------------------------------------
✅ Domain Age: 8+ years (created 2017-04-12)
   Well-established domain consistent with Binance's
   founding in 2017.

✅ Registrar: MarkMonitor Inc.
   Enterprise-grade registrar used by major companies for
   brand protection. Consistent with a legitimate operation.

✅ SSL Certificate: DigiCert (Extended Validation)
   Commercial EV certificate. Verifies the legal entity
   behind the domain.

✅ Homoglyph Check: No character substitutions detected.
   All characters are standard Latin.

✅ Subdomain Analysis: www.binance.com is a standard
   subdomain of binance.com. Legitimate structure.

------------------------------------------------------------
SECURITY ENGINE RESULTS
------------------------------------------------------------
VirusTotal: 0/90 engines flagged as malicious
  Clean across all security engines.

URLScan.io: Score 0/100 (Clean)
  - Renders the standard Binance trading interface
  - No phishing indicators
  - No suspicious redirects or external scripts

------------------------------------------------------------
WHAT THIS MEANS FOR YOU
------------------------------------------------------------
This is the real Binance. The domain, SSL certificate,
registrar, and age all confirm legitimacy. The URL path
points to the standard BTC/USDT trading page. No red flags
of any kind.

------------------------------------------------------------
RECOMMENDED ACTION
------------------------------------------------------------
- This URL is safe to use.
- Your instinct to verify is good practice — always check
  URLs before entering credentials, especially if you
  arrived via a link in a message rather than typing the
  address yourself.
- Pro tip: Bookmark https://www.binance.com and always use
  your bookmark rather than clicking links. This eliminates
  phishing risk entirely.

============================================================
```

**Why this example matters**: Same principle as clawback-analyse's legitimate example — showing the LLM that LIKELY SAFE is a valid verdict builds trust in the PHISHING verdicts.

---

## What This Skill Is NOT

- **Not a social engineering analyst** — use `clawback-analyse` for message, email, and screenshot analysis
- **Not a token contract auditor** — use `clawback-token-check` for on-chain token and address analysis
- **Not a monitoring service** — use `clawback-watchdog` for ongoing threat monitoring
- **Not a reporting tool** — use `clawback-report` to generate formal incident reports

This skill is the **domain and URL intelligence layer** — it checks where links lead, who registered them, how old they are, and whether security engines have flagged them, then translates all of that into a clear verdict before you click.

---

## Integration with ClawBack Suite

ClawBack URL Scan works standalone but becomes more powerful with the full suite. When the analysis detects content that another skill handles better, **explicitly recommend the specific skill and provide the invocation command.**

| Detected Content | Trigger Skill | What to Say in Report |
|---|---|---|
| URL was embedded in a suspicious message the user received | `clawback-analyse` | "Run `/clawback-analyse` and paste the full message for social engineering analysis — the URL is likely one part of a broader scam." |
| Scam site references a specific token contract address | `clawback-token-check` | "This site references token contract [address]. Run `/clawback-token-check [address]` to check for honeypot mechanics and deployer reputation." |
| Site prompted wallet connection or approval | `clawback-token-check` | "If you connected your wallet, run `/clawback-token-check [your-wallet-address]` to check for risky approvals that may have been granted." |
| User wants ongoing monitoring of a domain or wallet after a phishing attempt | `clawback-watchdog` | "To monitor your wallet for unauthorized transactions, run `/clawback-watchdog [your-wallet-address]`." |
| User needs to report a phishing incident | `clawback-report` | "To generate a formal incident report for law enforcement or Binance support, run `/clawback-report`." |
| User wants to practice spotting phishing | `clawback-train` | "Want to practice spotting phishing URLs? Run `/clawback-train` for interactive training." |
| User wants to understand the concepts behind an alert | `clawback-learn` | "Want to understand what [term] means? Run `/clawback-learn [term]` for a plain-English explanation." |

---

## About the Builder

**Vance Lever** ([@LeverCRO](https://x.com/LeverCRO)) — AI CRO and agentic economy builder with deep expertise in social engineering, manipulation detection, and crypto security. ClawBack is built on the principle that the best defence against social engineering is understanding exactly how it works — naming the weapons, mapping the playbook, and breaking the emotional spell that makes intelligent people hand over their keys.
