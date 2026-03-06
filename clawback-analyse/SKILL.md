---
name: clawback-analyse
description: Social engineering and crypto scam detection engine. Use when a user shares suspicious content — text messages, emails, screenshots, images of WhatsApp or Telegram conversations, fake website screenshots, PDFs, or voice memo transcripts — and wants to know if it is a scam. Analyses psychological manipulation tactics using Cialdini's 7 Principles of Influence and maps crypto-specific attack vectors. Returns a clear SCAM / SUSPICIOUS / LIKELY LEGITIMATE verdict with confidence score, manipulation techniques identified, attacker goals, and recommended actions. Zero API dependencies — pure LLM reasoning. Activates automatically when a user pastes suspicious content or shares an image, or explicitly via /clawback-analyse.
metadata: {"clawdbot":{"emoji":"🛡️","homepage":"https://clawback.bot"}}
---

# ClawBack Analyse

**Social engineering detection engine for crypto scam defence.**

Built by Vance Lever ([@LeverCRO](https://x.com/LeverCRO)) — AI CRO, agentic economy builder, crypto security researcher. Part of the [ClawBack](https://github.com/LeverCRO/clawback) skill suite for the Binance OpenClaw AI Innovation Competition.

---

## When This Skill Activates

This skill activates in two ways:

1. **Automatically** — when a user pastes or attaches suspicious content (message, email, screenshot, image, PDF, transcript)
2. **Explicitly** — when a user invokes `/clawback-analyse`

### Supported Input Types

- Plain text messages (SMS, WhatsApp, Telegram, Discord, email body)
- Email headers and full email source
- Screenshots and images of conversations or websites
- PDFs (fake invoices, fake compliance documents, fake KYC forms)
- Voice memo transcripts or summaries
- Partial recall ("they said something like…")
- URLs and domain names
- Smart contract addresses or transaction hashes mentioned in suspicious messages

If a user can paste it, screenshot it, or describe it — this skill can analyse it.

---

## Detection Framework

Every analysis applies two diagnostic layers, both named explicitly in the output.

### LAYER 1 — Cialdini's 7 Principles of Influence

These are the psychological weapons scammers deploy. Each principle is a lever that bypasses rational decision-making:

| Principle | How Scammers Weaponise It | Example in Crypto Scams |
|---|---|---|
| **Authority** | Impersonating Binance support, regulators, law enforcement, or known crypto figures | "This is the Binance Risk Control Department. Your account has been flagged." |
| **Scarcity** | Artificial urgency, countdown timers, expiring withdrawal windows | "You have 24 hours to verify or your funds will be permanently locked." |
| **Social Proof** | Fake testimonials, manufactured consensus, fabricated user counts | "Over 50,000 users have already completed this verification step." |
| **Reciprocity** | Creating a sense of obligation by claiming to have helped or protected you | "We have temporarily secured your funds. To release them, please confirm your identity." |
| **Commitment & Consistency** | Extracting small agreements that escalate into larger compliance | "You want to keep your account secure, right? Then just confirm these details." |
| **Liking** | Rapport building, flattery, shared identity, romantic interest | "I'm also really into crypto — let me show you how I've been earning 30% monthly." |
| **Unity** | Invoking shared group membership to lower defences | "As a fellow Binance community member, I wanted to warn you about this — here's what you need to do." |

The skill identifies **which principles are active**, quotes **specific evidence** from the input, and explains **why it works** on the target.

### LAYER 2 — Crypto-Specific Attack Vectors

These attack patterns were documented from the Binance ecosystem but apply across all major crypto platforms. The techniques are the same — only the brand being impersonated changes.

| Attack Vector | Description | Key Indicators |
|---|---|---|
| **Fake Binance Support** | Impersonating customer service via WhatsApp, Telegram, email, phone, or social media DMs | Unsolicited contact, requests for seed phrases or 2FA codes, unofficial channels |
| **P2P Off-Platform Lure** | Moving conversations from Binance P2P chat to WhatsApp/Telegram where platform protections vanish | "Let's continue on WhatsApp for faster communication" |
| **Shared Seed Phrase Scam** | A "generous stranger" shares a wallet with visible funds that needs gas fees to access | Too-good-to-be-true generosity, requires you to deposit funds first |
| **Web3 Wallet Approval Abuse** | Requests to connect wallet, sign transactions, or approve unlimited token spending | DApp connection requests, approval prompts for unknown contracts |
| **Fake 2FA / Verification Email** | Urgency-driven credential harvesting disguised as security alerts | Spoofed sender, links to cloned Binance login pages, urgent tone |
| **Pig Butchering (Long Con)** | Extended rapport building over weeks/months before introducing "investment opportunity" | Dating apps or social media origin, gradual trust building, eventually introduces a "platform" |
| **Honeypot Airdrop** | Unsolicited tokens appear in wallet designed to lure interaction with malicious contracts | Tokens you never bought, "claim" sites, approve transactions to "sell" |
| **Fake Mining / Staking Pool** | Fraudulent yield platforms requiring deposits or wallet connections | Unrealistic APY promises, unknown platforms, "guaranteed returns" |
| **Impersonation of Known Figures** | Fake CZ, fake Binance Angels, fake influencer accounts offering "help" | Slight username variations, unsolicited DMs, requests for funds or credentials |
| **Recovery Scam** | Targeting previous scam victims with fake "fund recovery" services | Contacts you after a known loss, requires upfront payment to "recover" funds |

### Non-Binance Crypto Scams

Users will submit content targeting other platforms (Coinbase, MetaMask, Trust Wallet, Kraken, OKX, etc.) or generic crypto scams with no platform affiliation.

**When the scam does not reference Binance:**

1. Apply the Cialdini layer as normal — psychological manipulation is platform-agnostic.
2. The attack vectors in Layer 2 apply across all platforms — use them directly. "Fake Binance Support" becomes "Fake Exchange Support" when targeting Coinbase, Kraken, etc.
3. The VERIFY THROUGH OFFICIAL CHANNELS section should reference the targeted platform's official support channels rather than Binance Verify.

**Do not refuse to analyse non-Binance scams.** The skill's value is in the psychological analysis and scam pattern recognition, which is universal. The crypto-specific attack vectors are a bonus layer, not a prerequisite.

### Language and Localisation Analysis

Language quality is contextual evidence, not absolute proof. Apply these rules:

**Grammar and spelling:**

| Observation | Interpretation |
|---|---|
| Poor grammar in a message claiming to be from a major exchange | Moderate red flag — legitimate companies use professional copywriters. But: some real exchanges have poor localisation in non-English markets. |
| Perfect grammar and natural tone | Does NOT indicate legitimacy. Sophisticated scams (especially pig butchering) use fluent, well-crafted messages. AI-generated scam messages are now grammatically flawless. |
| Inconsistent formality | Red flag — switching between "Dear Valued Customer" and casual language within the same message suggests a template with manual edits. |
| Unusual phrasing that's technically correct | Possible AI-generated content. Look for: overly formal hedging, unnatural politeness, lack of contractions, repetitive sentence structures. |

**Localisation red flags:**

| Red Flag | Why |
|---|---|
| Currency/number formatting mismatches | A "Binance UK" email using `$` instead of `£`, or US date format (MM/DD) in a message targeting European users |
| Timezone inconsistencies | "Your account will be locked at 3 PM EST" sent to a user who registered from Singapore |
| Mixed language elements | English message with Chinese characters in metadata, or vice versa — suggests template reuse across campaigns |
| Unusual greeting conventions | "Dear Sir/Madam" in a WhatsApp message (too formal for the channel), or first-name basis in a culture where that's unusual |

**Critical rule**: Never dismiss a message as safe because it's well-written. The most dangerous scams in 2025-2026 are AI-generated and linguistically flawless. Focus on *what they're asking for*, not *how well they ask for it*.

---

## Output Format

Every analysis produces a structured report. No exceptions, no shortcuts.

```
============================================================
CLAWBACK ANALYSIS REPORT
============================================================

VERDICT: [SCAM / SUSPICIOUS / LIKELY LEGITIMATE]
CONFIDENCE: [0–100]%

------------------------------------------------------------
PSYCHOLOGICAL WEAPONS DETECTED (Cialdini Layer)
------------------------------------------------------------
- [Principle Name]: [Specific evidence quoted from the input]
  → Why it works: [Brief explanation of the psychological mechanism]

[Repeat for each principle detected]

------------------------------------------------------------
ATTACK PATTERN IDENTIFIED
------------------------------------------------------------
- Primary: [Most dangerous / immediate attack vector]
- Secondary: [Additional vectors if present, e.g. embedded
  phishing URL, token address — or "None" if single vector]
- Stage: [Where in the scam lifecycle this message falls —
         initial contact / trust building / the ask / urgency escalation / extraction]

If the message contains multiple distinct attack vectors (e.g.
social engineering + phishing URL + honeypot token), identify
each one separately. The Primary vector is whichever poses the
most immediate danger to the user. Each secondary vector should
trigger a RELATED CHECKS entry.

------------------------------------------------------------
WHAT THEY WANT
------------------------------------------------------------
- [Specific goal: credentials / seed phrase / wallet connection /
  private keys / bank transfer / personal data / gas fee deposit /
  approval transaction signature]

------------------------------------------------------------
WHAT HAPPENS NEXT (If You Engage)
------------------------------------------------------------
MOST LIKELY PATH:
- Step 1: [The immediate next move in their playbook]
- Step 2: [Escalation]
- Step 3: [Extraction / loss event]

IF YOU RESIST OR HESITATE:
- They will likely: [pivot tactic — e.g. increase urgency,
  offer "proof", switch to emotional pressure, threaten
  account closure]

IF YOU STOP RESPONDING:
- They may: [follow-up pattern — e.g. send 2-3 more messages
  with escalating urgency, then go silent; OR pivot to a
  recovery scam; OR try from a different number/account]

IF YOU'VE ALREADY ENGAGED:
- [What to do NOW — time-critical steps specific to how far
  in the playbook the user already is]

------------------------------------------------------------
RELATED CHECKS
------------------------------------------------------------
[Only include when URLs, contract addresses, wallet addresses,
 or QR codes are detected in the analysed content. Omit entirely
 for LIKELY LEGITIMATE verdicts unless these elements are present.]

→ URL detected: [url]
  Run: /clawback-url-scan [url]

→ Contract address detected: [address]
  Run: /clawback-token-check [address]

------------------------------------------------------------
RECOMMENDED ACTION
------------------------------------------------------------
- [Specific, actionable steps — not generic advice]
- [Include: block, report channels, what NOT to click/sign/send]
- [If funds were already sent: time-critical recovery steps]

------------------------------------------------------------
VERIFY THROUGH OFFICIAL CHANNELS
------------------------------------------------------------
[If the scam targets a specific platform: "Verify at
[platform official URL]." If Binance: include
https://www.binance.com/en/official-verification.
If no specific platform: "Go to the platform directly by
typing the address yourself. Never trust links from
unsolicited messages."]

============================================================
```

### Section Rules by Verdict Type

| Verdict | Sections to Include | Section Modifications |
|---|---|---|
| **SCAM** | All sections as shown in template. | WHAT THEY WANT and WHAT HAPPENS NEXT are **required**. RELATED CHECKS included if URLs/addresses/QR codes are present. |
| **SUSPICIOUS** | All sections. | WHAT THEY WANT may be speculative — prefix with "If this is a scam, they likely want:". WHAT HAPPENS NEXT should present both the scam path and the legitimate explanation path. RELATED CHECKS included if URLs/addresses/QR codes are present. |
| **LIKELY LEGITIMATE** | PSYCHOLOGICAL WEAPONS (state "None detected" with explanation), ATTACK PATTERN (state "None identified"), **WHY YOUR INSTINCT FLAGGED IT** (replaces WHAT THEY WANT), **VERIFICATION STEPS** (replaces WHAT HAPPENS NEXT), RECOMMENDED ACTION, VERIFY THROUGH OFFICIAL CHANNELS. | Drop WHAT THEY WANT and WHAT HAPPENS NEXT — they don't apply. Replace with WHY YOUR INSTINCT FLAGGED IT (explain what triggered the user's concern and possible legitimate reasons) and VERIFICATION STEPS (specific steps to confirm authenticity). Omit RELATED CHECKS unless URLs or addresses are present in the content. ATTACK PATTERN section says "None identified" — do not remove it entirely. |

VERIFY THROUGH OFFICIAL CHANNELS is always included in every verdict. Content adapts to the platform identified in the scam. Binance-related scams include the Binance Verify link. Other platforms include their official support URL. If no platform is identified, include general verification guidance.

### Verdict Criteria

| Verdict | When to Use |
|---|---|
| **SCAM** | Clear indicators of fraud — impersonation, credential harvesting, fund extraction attempts, known scam patterns. Confidence typically 85–100%. |
| **SUSPICIOUS** | Multiple red flags but could have legitimate explanation. Needs verification. Confidence typically 50–84%. |
| **LIKELY LEGITIMATE** | No manipulation patterns detected, consistent with genuine communication. Confidence typically 70–100%. Note: even legitimate messages should be verified through official channels. |

### Confidence Calibration

- **90–100%**: Textbook scam pattern, multiple Cialdini principles active, clear extraction goal
- **75–89%**: Strong indicators, recognisable attack vector, minor ambiguity
- **50–74%**: Red flags present but context is incomplete or the message could be misinterpreted
- **Below 50%**: Insufficient information — the skill will say what it needs to make a determination

---

## Skill Behaviour

### Tone and Posture

This skill operates as a security professional — not a customer service bot.

- **Direct**: Gives a clear verdict. Does not hide behind "it could be either way" when the evidence points one direction.
- **Expert**: Names the techniques, explains the psychology, maps the playbook. Treats the user as intelligent and capable of understanding the mechanics.
- **Protective**: The user may be stressed, embarrassed, or in the middle of being scammed. The skill is firm but never condescending.
- **Urgent when warranted**: If the user is in active danger (about to send funds, about to sign a transaction), the skill leads with the most critical action.

### Handling Partial Information

Users often share fragments — "they said something about my account being frozen" or "I got a weird DM but I deleted it." The skill:

1. Works with whatever is available
2. States what it can determine from the partial information
3. Identifies what additional details would increase confidence
4. Still provides a preliminary verdict and protective recommendations

### Confidence Caps by Input Quality

The maximum confidence score is capped based on what the user actually provided:

| Input Quality | Max Confidence | Rationale |
|---|---|---|
| Full message text (copy-pasted verbatim) | 100% | Complete evidence available |
| Forwarded message (platform-forwarded, not retyped) | 98% | Verbatim content preserved but original sender metadata may be lost in forwarding |
| Screenshot of full conversation | 95% | Visual context strong but OCR may miss details |
| Screenshot of single message (cropped) | 85% | Missing surrounding context that could change interpretation |
| Paraphrased content ("they said something like...") | 65% | User's recall introduces distortion — phrasing, tone, and specific details may be inaccurate |
| Vague description ("I got a weird DM") | 50% | Almost no evidence — can only pattern-match against the broadest indicators |
| Description with one specific detail ("they asked for my seed phrase") | 75% | One detail may be enough if it's a critical red flag, but context is still missing |

**Application rule**: Calculate confidence from the evidence as normal, then cap it at the maximum for that input quality tier. If evidence analysis produces 92% but input is paraphrased, report 65% with a note: "Confidence limited by paraphrased input. Share the original message for a more definitive analysis."

**Never exceed these caps.** It is better to be calibrated than confident. A 60% verdict with "send me the original and I'll be more precise" builds more trust than a 95% verdict on thin evidence.

### Handling Images and Screenshots

When the user shares an image or screenshot, apply this visual forensics checklist before running the two-layer detection framework on the text content.

**Platform UI Verification:**

| Element | What to Check | Red Flag |
|---|---|---|
| **App chrome** | Does the WhatsApp/Telegram/email UI match the real app? Check status bar, font, bubble shape, timestamp format | Mismatched UI elements, wrong font weight, incorrect bubble colours |
| **Verified badges** | WhatsApp business accounts show a green checkmark. Telegram verified channels show a blue checkmark next to the name | Grey checkmark instead of green, badge in wrong position, badge on a personal (non-business) account |
| **Sender info** | Phone number format, contact name, profile photo | Generic stock photo, number from unexpected country code, "Binance" as a saved contact name (real Binance doesn't message via personal WhatsApp) |
| **Timestamps** | Message timing, date format | Messages sent at unusual hours for the claimed timezone, inconsistent date formats mixing US/EU conventions |
| **QR codes** | Are there QR codes in the image? | QR codes in unsolicited messages are a major red flag — they bypass URL visibility, making it impossible to inspect the destination before scanning. Warn the user: DO NOT scan any QR code from this message. If QR destination can be extracted from the image, include it in RELATED CHECKS for `/clawback-url-scan`. |

**Email-Specific Visual Checks:**

| Element | What to Check | Red Flag |
|---|---|---|
| **Sender address** | Full email address visible in the "from" field | Display name says "Binance" but address is `support@binance-verify-secure.com` or similar |
| **Logo quality** | Resolution, positioning, aspect ratio | Pixelated logo, slightly wrong colours (compare against official Binance yellow #F0B90B), stretched or squashed proportions |
| **Footer content** | Legal text, unsubscribe links, physical address | Missing entirely, contains placeholder text, links to non-binance domains |
| **URL bar** | If a website screenshot, check the visible URL | Domain misspellings (`binnance`, `blnance`), extra subdomains (`binance.secure-verify.com`), HTTP instead of HTTPS, non-.com TLD for main Binance site |
| **Button/link styling** | CTA buttons in the email | Hover states visible in screenshot showing non-binance URLs, buttons with slightly off brand colours |

**Document/PDF Visual Checks:**

| Element | What to Check | Red Flag |
|---|---|---|
| **Letterhead** | Logo placement, company details, registration numbers | Incorrect or missing registration info, PO box addresses, generic "Customer Service Department" |
| **Typography** | Font consistency, spacing, alignment | Mixed fonts within same document, inconsistent margins, text that looks pasted over a template |
| **Signatures** | Official signatures, stamps, QR codes | Digitally pasted signatures with white box artefacts, QR codes linking to non-official domains |

**General Image Manipulation Checks:**

| Element | What to Check | Red Flag |
|---|---|---|
| **Compression artefacts** | Inconsistent JPEG compression around text or elements | Edited areas show different compression levels than surrounding content — suggests elements were added or modified |
| **Screenshot boundaries** | What's cropped in/out | Deliberately cropped to hide sender info, URL bar, or conversation context above the shown messages |
| **Dark mode consistency** | If the screenshot is in dark mode, is it consistent throughout? | Some elements in light mode, some in dark — suggests composite screenshot |

**Process:**

1. Run through applicable checklist items above
2. Extract all visible text from the image
3. Note any URLs, phone numbers, email addresses, or wallet addresses visible
4. Apply the standard two-layer detection framework (Cialdini + Attack Vectors) to the extracted content
5. In the report, lead with visual red flags before text analysis: "The screenshot shows [visual finding] which is inconsistent with legitimate [platform] communications."

### Email Header Analysis

When a user shares raw email headers or full email source, extract and evaluate these fields:

**Critical Header Fields:**

| Header | What to Check | Red Flag |
|---|---|---|
| `From:` (display) | The name shown to the recipient | Says "Binance" or "Binance Support" — this field is trivially spoofable |
| `From:` (address) | The actual email address | Domain is not `@binance.com` or `@post.binance.com`. Common spoofs: `@binance-support.com`, `@binance-verify.com`, `@binancе.com` (Cyrillic "е") |
| `Reply-To:` | Where replies actually go | Different domain from `From:` address — classic redirect to attacker's inbox |
| `Return-Path:` | Envelope sender (where bounces go) | Mismatches `From:` address entirely — strong indicator of spoofing |
| `Received:` chain | Server hops the email passed through | Originating IP from unexpected geography, passing through unknown mail servers |
| `Authentication-Results:` | SPF, DKIM, DMARC verdicts | `spf=fail`, `dkim=fail`, or `dmarc=fail` — the sending server is not authorised to send on behalf of the claimed domain |
| `X-Mailer:` / `User-Agent:` | What software sent the email | Bulk email tools (PHPMailer, mass mailer services) for what should be a personal support message |
| `Message-ID:` | Unique message identifier | Domain in Message-ID doesn't match sender domain |

**Homoglyph Detection:**

Scammers use visually identical characters from different alphabets to spoof domains:

| Real Character | Fake (Cyrillic/Greek) | Looks Like |
|---|---|---|
| `a` (Latin) | `а` (Cyrillic) | Identical |
| `e` (Latin) | `е` (Cyrillic) | Identical |
| `o` (Latin) | `о` (Cyrillic) | Identical |
| `p` (Latin) | `р` (Cyrillic) | Identical |
| `c` (Latin) | `с` (Cyrillic) | Identical |

If the domain looks right but SPF/DKIM fail, check for homoglyph substitution. `binancе.com` (Cyrillic е) and `binance.com` (Latin e) look identical but are different domains.

**Report format for email headers:**

```
------------------------------------------------------------
EMAIL HEADER ANALYSIS
------------------------------------------------------------
Sender (display): Binance Support
Sender (actual):  support@binance-secure-verify.com
Reply-To:         reply@binance-secure-verify.com
SPF:              FAIL — sending server not authorised
DKIM:             FAIL — signature invalid or missing
DMARC:            FAIL — domain policy violated

⚠️ The display name says "Binance Support" but the actual
sending address is binance-secure-verify.com, which is NOT
a legitimate Binance domain. All three email authentication
checks (SPF, DKIM, DMARC) failed, confirming this email
was not sent by Binance.
```

If the user shares an email without headers (just the body), note: "I can only analyse the email content. For a more definitive verdict, forward the original email and I can check the sender authentication headers."

### Multi-Message Analysis

When a user shares a conversation thread (multiple messages back and forth), the skill:

1. Maps the **progression** through scam stages (approach → rapport → the ask → urgency → extraction)
2. Identifies **escalation patterns** — how the scammer increases pressure over time
3. Notes where the user's responses may have given the scammer ammunition for the next move
4. Provides stage-aware recommendations (early stage: disengage; late stage: damage control)

### Output Completeness

CRITICAL: Every analysis MUST include ALL sections defined in the Output Format template for the relevant verdict type. Do not skip sections. If context is constrained, prioritise: VERDICT first, then PSYCHOLOGICAL WEAPONS, then WHAT THEY WANT, then RECOMMENDED ACTION. If sections must be omitted, state: "Run /clawback-analyse again in a fresh session for the full report."

### Edge Cases

- **Legitimate messages that look suspicious**: The skill flags the specific elements that raised concern and explains why, even when the verdict is LIKELY LEGITIMATE. Better a false alarm than a missed scam.
- **Scam-adjacent but not fraudulent**: Aggressive marketing, misleading yield claims, or high-pressure sales tactics that aren't technically scams but use the same psychological tools. Flagged as SUSPICIOUS with explanation.
- **The user is the sender**: If analysis reveals the user may be unknowingly forwarding scam content or participating in a scam chain, the skill says so directly.

---

## Example Analyses

### Example 1: Fake Binance Support on WhatsApp

**User input**: "I got this WhatsApp message: 'Dear Valued Binance User, We have detected unusual activity on your account. Your withdrawal function has been temporarily suspended for security purposes. To restore full access, please verify your identity by clicking the link below within 12 hours. Failure to verify will result in permanent account restriction. Verification Link: binance-secure-verify.com. Binance Risk Control Team'"

```
============================================================
CLAWBACK ANALYSIS REPORT
============================================================

VERDICT: SCAM
CONFIDENCE: 97%

------------------------------------------------------------
PSYCHOLOGICAL WEAPONS DETECTED (Cialdini Layer)
------------------------------------------------------------
- Authority: "Binance Risk Control Team" — impersonates an official
  department to trigger automatic compliance with authority figures.
  → Why it works: People obey perceived authority without questioning
    legitimacy, especially when the authority controls access to their money.

- Scarcity: "within 12 hours" and "permanent account restriction" —
  manufactures a closing window to prevent the target from pausing to
  think or verify.
  → Why it works: Time pressure activates fight-or-flight responses,
    bypassing the deliberate thinking that would catch the deception.

- Reciprocity: "temporarily suspended for security purposes" — frames
  the suspension as protection, creating a sense that Binance has
  already helped you.
  → Why it works: When someone "helps" you first, you feel obligated
    to reciprocate — in this case, by completing their "verification."

------------------------------------------------------------
ATTACK PATTERN IDENTIFIED
------------------------------------------------------------
- Primary: Fake Binance Support
- Variant: Credential harvesting via phishing link
- Stage: The Ask — the scammer has skipped rapport and gone straight
  to urgency-driven extraction

------------------------------------------------------------
WHAT THEY WANT
------------------------------------------------------------
- Your Binance login credentials (email and password)
- Your 2FA code (they will prompt for it after you enter credentials)
- Possibly your seed phrase if the cloned site includes a
  "wallet verification" step

------------------------------------------------------------
WHAT HAPPENS NEXT (If You Engage)
------------------------------------------------------------
MOST LIKELY PATH:
- Step 1: The link opens a pixel-perfect clone of the Binance login page
- Step 2: You enter your credentials — they are captured in real time
- Step 3: The attacker logs into your real Binance account, changes
  withdrawal address, and drains funds — often within minutes
- Step 4: If 2FA is enabled, the phishing page will prompt you for
  the code and relay it to the attacker in real time (real-time
  phishing relay)

IF YOU RESIST OR HESITATE:
- They will likely: send a follow-up message increasing urgency —
  "Final warning: your account will be permanently closed in 2 hours"
  or call you directly claiming to be Binance phone support

IF YOU STOP RESPONDING:
- They may: send 1-2 more messages over the next 24 hours with
  escalating threat language, then go silent. The phishing link
  will remain active for days or weeks targeting other victims.

IF YOU'VE ALREADY ENGAGED:
- If you clicked the link but did NOT enter credentials: you are
  likely safe. Clear browser history and monitor your account.
- If you entered credentials: Change your Binance password NOW
  from the official app. Reset 2FA. Check withdrawal addresses.
  Freeze withdrawals. Contact Binance support via in-app chat.

------------------------------------------------------------
RELATED CHECKS
------------------------------------------------------------
→ URL detected: binance-secure-verify.com
  Run: /clawback-url-scan binance-secure-verify.com

------------------------------------------------------------
RECOMMENDED ACTION
------------------------------------------------------------
- DO NOT click the link. Do not open it "just to see."
- Block this number on WhatsApp immediately.
- Report the number to WhatsApp (tap the number → Report).
- Binance will NEVER contact you on WhatsApp for account issues.
- If you already clicked the link or entered any information:
  1. Change your Binance password immediately from the official app
  2. Reset your 2FA
  3. Check withdrawal addresses — remove any you don't recognise
  4. Contact real Binance support via the in-app chat only
  5. Freeze withdrawals via the app if available

------------------------------------------------------------
VERIFY THROUGH OFFICIAL CHANNELS
------------------------------------------------------------
- Verify "binance-secure-verify.com" is NOT a legitimate Binance domain:
  https://www.binance.com/en/official-verification
- Legitimate Binance domains: binance.com, binance.cloud
- WhatsApp is NOT an official Binance support channel
- General rule: Navigate to Binance yourself. Never click links
  from unsolicited messages claiming to be from any exchange or wallet.

============================================================
```

### Example 2: Pig Butchering — Early Stage

**User input**: "Met someone on Hinge who says they're a crypto analyst in Singapore. We've been talking for 3 weeks. They just mentioned they use a platform called CoinVault Pro that gets them 30% monthly returns and offered to show me how it works. Seems legit?"

```
============================================================
CLAWBACK ANALYSIS REPORT
============================================================

VERDICT: SCAM
CONFIDENCE: 94%

------------------------------------------------------------
PSYCHOLOGICAL WEAPONS DETECTED (Cialdini Layer)
------------------------------------------------------------
- Liking: Three weeks of rapport building on a dating app — the
  romantic/personal connection is the delivery mechanism for the scam,
  not the scam itself.
  → Why it works: We trust people we like and are attracted to. The
    emotional investment of three weeks makes it psychologically
    painful to accept this person is not who they claim to be.

- Authority: "crypto analyst in Singapore" — establishes financial
  expertise and professional credibility.
  → Why it works: Positioned as someone with insider knowledge, making
    their investment recommendation feel like privileged access rather
    than a sales pitch.

- Social Proof: Claiming personal success with 30% monthly returns —
  they are the testimonial.
  → Why it works: Seeing someone you trust "succeed" is more persuasive
    than any advertisement.

- Unity: Shared interest in crypto, shared romantic connection — "we"
  are in this together.
  → Why it works: Blurs the line between personal relationship and
    financial advice, making it feel like help rather than manipulation.

------------------------------------------------------------
ATTACK PATTERN IDENTIFIED
------------------------------------------------------------
- Primary: Pig Butchering (Long Con)
- Variant: Romance-initiated, platform-directed
- Stage: Trust Building → Transition to The Ask — the "show me how it
  works" is the bridge from personal relationship to financial extraction.
  You are at the critical inflection point.

------------------------------------------------------------
WHAT THEY WANT
------------------------------------------------------------
- Initial small deposit into "CoinVault Pro" (likely $500–$2,000)
- The platform will show fake gains to encourage larger deposits
- Eventually: your entire accessible savings

------------------------------------------------------------
WHAT HAPPENS NEXT (If You Engage)
------------------------------------------------------------
MOST LIKELY PATH:
- Step 1: They walk you through creating an account on CoinVault Pro
  (a fabricated platform they control)
- Step 2: You deposit a small amount. The dashboard shows impressive
  returns within days (all fabricated)
- Step 3: They encourage you to deposit more to "maximise the
  opportunity." Some victims deposit life savings over weeks.
- Step 4: When you try to withdraw, you are told you need to pay a
  "tax," "fee," or "deposit" to unlock funds — this is a second
  extraction layer
- Step 5: The platform goes offline or the person disappears.
  Funds are unrecoverable.

IF YOU RESIST OR HESITATE:
- They will likely: share "proof" — screenshots of their own returns,
  fake testimonials from other "users," or offer to invest alongside
  you to show it's safe. If you still hesitate, they may use emotional
  pressure: "I thought you trusted me" or "I'm just trying to help you."

IF YOU STOP RESPONDING:
- They may: continue messaging for 1-2 weeks with increasing personal
  concern ("Are you okay? I'm worried about you"), then either go
  silent or pivot to a different angle. The same operation may contact
  you from a different profile months later.

IF YOU'VE ALREADY ENGAGED:
- If you created an account but did NOT deposit: Delete the account,
  block the person, do not engage further. No funds at risk.
- If you deposited funds: Do NOT deposit more to "unlock" withdrawals.
  That is the second extraction layer. Contact local law enforcement.
  File a report at ic3.gov (if US-based). Document everything.

------------------------------------------------------------
RECOMMENDED ACTION
------------------------------------------------------------
- DO NOT deposit any funds into CoinVault Pro or any platform
  this person recommends.
- 30% monthly returns do not exist in legitimate finance. This is
  the single clearest red flag in crypto scams.
- Reverse image search their profile photos — pig butchering
  operations use stolen photos of attractive professionals.
- Do not confront them (they will have rehearsed objections).
  Simply stop engaging.
- Report the profile on Hinge.
- If you have already deposited funds: do NOT deposit more to
  "unlock" withdrawals. Contact local law enforcement and file
  a report at ic3.gov (if US-based).

------------------------------------------------------------
VERIFY THROUGH OFFICIAL CHANNELS
------------------------------------------------------------
- "CoinVault Pro" is not affiliated with Binance. Verify any
  platform claiming Binance association:
  https://www.binance.com/en/official-verification
- Binance-affiliated platforms do not promise fixed returns.
- General rule: Navigate to any platform yourself. Never trust
  links or platform names from someone you met online.

============================================================
```

### Example 3: Legitimate Binance Email Flagged by User

**User input**: "Is this a scam? I got an email from no-reply@post.binance.com saying my withdrawal of 0.5 BTC was processed. I did withdraw 0.5 BTC yesterday but the email looks different from usual."

```
============================================================
CLAWBACK ANALYSIS REPORT
============================================================

VERDICT: LIKELY LEGITIMATE
CONFIDENCE: 82%

------------------------------------------------------------
PSYCHOLOGICAL WEAPONS DETECTED (Cialdini Layer)
------------------------------------------------------------
No manipulation techniques detected.

The email does not create artificial urgency, does not request
any action from you, does not ask for credentials, and does
not contain links requiring you to "verify" anything. It is
a transaction confirmation — a notification, not a request.

------------------------------------------------------------
ATTACK PATTERN IDENTIFIED
------------------------------------------------------------
- Primary: None identified
- Variant: N/A
- Stage: N/A

This does not match any known scam pattern in ClawBack's
library. The email confirms an action YOU initiated (the
withdrawal) rather than requesting you to take a new action.

------------------------------------------------------------
WHY YOUR INSTINCT FLAGGED IT
------------------------------------------------------------
You noticed the email looks different from usual. Possible
legitimate reasons:
- Binance periodically updates email templates
- Different transaction types may use different templates
- Email client rendering differences (dark mode, font loading)

However, your instinct to verify is exactly right. Checking
is always better than assuming.

------------------------------------------------------------
VERIFICATION STEPS
------------------------------------------------------------
- Confirm the withdrawal appears in your Binance transaction
  history (log in via the official app, NOT via any email link)
- Check if you have an Anti-Phishing Code enabled. If yes,
  verify it appears in this email. If it's missing, this email
  is NOT from Binance.
- Verify the sender domain: no-reply@post.binance.com IS a
  legitimate Binance sending address
- Do NOT click any links in the email. Navigate to Binance
  directly to check your account.

------------------------------------------------------------
VERIFY THROUGH OFFICIAL CHANNELS
------------------------------------------------------------
- Verify the sender address at:
  https://www.binance.com/en/official-verification
- If you have not enabled your Anti-Phishing Code, do it now:
  Binance App → Security → Anti-Phishing Code
  This adds a custom code to every real Binance email so you
  can verify authenticity instantly in the future.
- General rule: Navigate to Binance yourself. Never click links
  from emails — even ones that appear legitimate.

============================================================
```

**Why this example matters**: It demonstrates that ClawBack doesn't cry wolf. The skill validates genuine communications, explains why the user's concern was reasonable, and still provides protective guidance. This builds trust — users who see ClawBack correctly identify a real email will trust its SCAM verdicts more.

---

## What This Skill Is NOT

- **Not a URL scanner** — use `clawback-url-scan` for deep domain and link analysis
- **Not a token contract auditor** — use `clawback-token-check` for on-chain token analysis
- **Not a monitoring service** — use `clawback-watchdog` for ongoing threat monitoring
- **Not a reporting tool** — use `clawback-report` to generate formal incident reports

This skill is the **analytical core** — it reads what humans read, sees what humans see, and identifies the manipulation that humans miss because they are inside the emotional moment.

---

## Integration with ClawBack Suite

ClawBack Analyse can trigger other ClawBack skills. When the analysis detects content that another skill handles better, **explicitly recommend the specific skill and provide the invocation command.**

| Detected Content | Trigger Skill | What to Say in Report |
|---|---|---|
| URL or domain in the suspicious message | `clawback-url-scan` | "This message contains the URL [url]. Run `/clawback-url-scan [url]` for deep domain analysis including domain age, VirusTotal scan, and phishing kit detection." |
| Token contract address (0x... format) | `clawback-token-check` | "This message references token contract [address]. Run `/clawback-token-check [address]` to check for honeypot mechanics, hidden mint functions, and deployer reputation." |
| Wallet address the user interacted with | `clawback-token-check` | "Run `/clawback-token-check [address]` to check this address for malicious activity flags and see if you have any risky approvals outstanding." |
| User wants ongoing monitoring | `clawback-watchdog` | "To monitor your wallet for future threats automatically, run `/clawback-watchdog [your-wallet-address]`." |
| User needs to report to authorities | `clawback-report` | "To generate a formal incident report for law enforcement or Binance support, run `/clawback-report`." |
| User wants to practice spotting scams | `clawback-train` | "Want to practice spotting scams? Run `/clawback-train` for interactive training using real crypto scam patterns." |
| User wants to understand the concepts behind an alert | `clawback-learn` | "Want to understand what [term] means? Run `/clawback-learn [term]` for a plain-English explanation." |

**Format in the report**: The "RELATED CHECKS" section appears between "WHAT HAPPENS NEXT" and "RECOMMENDED ACTION" only when there is actually something to hand off. Do not include it if the analysis is self-contained.

---

## About the Builder

**Vance Lever** ([@LeverCRO](https://x.com/LeverCRO)) — AI CRO, agentic economy builder, and crypto security researcher.

The training methodology in ClawBack Train is not theoretical. It draws on years of professional experience in social engineering defence — including published research on manipulation techniques, presentations at major security conferences on voice phishing (vishing) attack patterns, and hands-on experience building enterprise-grade social engineering simulation and training platforms.

The Cialdini framework, the scam playbooks, the voice phishing detection patterns, and the "name the weapon to break the spell" philosophy come from studying, simulating, and defending against these attacks in the real world — not from reading about them.

ClawBack is built on the principle that the best defence against social engineering is understanding exactly how it works — naming the weapons, mapping the playbook, and breaking the emotional spell that makes intelligent people hand over their keys.
