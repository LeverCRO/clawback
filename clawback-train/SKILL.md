---
name: clawback-train
description: Interactive crypto security training and scam detection practice. Three modes — Quick Quiz (5 rounds of "is this a scam?" with real patterns), Security Setup (guided walkthrough of exchange security features), and Personal Training (contextual scenarios using your own wallet data). For educational content (key terms, scam types, security concepts), see /clawback-learn. Zero API dependencies. Activates via /clawback-train.
metadata: {"clawdbot":{"emoji":"🎯","homepage":"https://clawback.bot"}}
---

# ClawBack Train

**Interactive crypto security training and scam detection practice.**

Built by Vance Lever ([@LeverCRO](https://x.com/LeverCRO)) — AI CRO, agentic economy builder, crypto security researcher. Part of the [ClawBack](https://github.com/LeverCRO/clawback) skill suite for the Binance OpenClaw AI Innovation Competition.

Zero API dependencies — pure LLM reasoning and education. The only external data it uses is watchdog state (for Personal Training mode), stored in OpenClaw memory.

---

## When This Skill Activates

This skill activates **explicitly only** — when a user invokes `/clawback-train` or any subcommand.

### Commands

| Command | Action |
|---|---|
| `/clawback-train` | Show mode selection menu |
| `/clawback-train quiz` | Quick Quiz — 5 rounds |
| `/clawback-train setup` | Security Setup — guided hardening checklist |
| `/clawback-train personal` | Personal Training — requires active watchdog |

---

## Mode Selection Menu

When a user runs `/clawback-train` with no arguments:

```
============================================================
CLAWBACK TRAINING
============================================================

Choose your training mode:

LEARN — Key terms, scam types, and how crypto security works
   → /clawback-learn
   (Separate skill — keeps this session focused on practice)

QUIZ — 5 rounds of "Is this a scam?" with real patterns
   Best for: Testing and sharpening your instincts.
   → /clawback-train quiz

SETUP — Harden your exchange security in 5 minutes
   Best for: Making sure your account settings are locked down.
   → /clawback-train setup

PERSONAL — Training scenarios based on YOUR wallet
   Best for: Learning from your own on-chain activity.
   Requires: /clawback-watchdog active
   → /clawback-train personal

BRIEFING — Current crypto scam landscape overview
   → /clawback-learn briefing
   (In the Learn skill — educational reference content)

Recommended path for new users: /clawback-learn → Quiz → Setup

============================================================
```

If the user has previous training history (stored in OpenClaw memory), append a progress summary below the menu. See [Scoring & Progression](#scoring--progression) for the format.

---

## Mode 1: Quick Quiz

5 rounds of "Is this a scam?" Each round presents a realistic scenario. The user answers. The skill scores them and explains the answer.

### Quiz Structure

**Selection rules**: Pick 5 scenarios per session from the 14-scenario library below. Mix: 1 at difficulty 1, 2 at difficulty 2, 1 at difficulty 3, and 1 legitimate. Difficulty should escalate — round 1 easiest, round 5 hardest. Never the same 5 in a row if the user runs multiple sessions.

**Round format:**

```
============================================================
CLAWBACK TRAINING — ROUND [X] of 5
============================================================

[Scenario description — realistic message, email, or situation]

Is this a SCAM or LEGITIMATE?
============================================================
```

**After user answers:**

```
============================================================
ROUND [X] — [CORRECT / INCORRECT]
============================================================

ANSWER: [SCAM / LEGITIMATE]
YOUR ANSWER: [what they said]

------------------------------------------------------------
WHY
------------------------------------------------------------
[Explanation using Cialdini principles and attack vector names.
 For scams: techniques used, what the attacker wanted, what
 would happen next.
 For legitimate: what made it look suspicious and why those
 elements had innocent explanations.]

------------------------------------------------------------
THE TELL
------------------------------------------------------------
[The single most reliable indicator. One thing to remember.
 This is the key learning moment.]

------------------------------------------------------------
LEARN MORE
------------------------------------------------------------
→ /clawback-learn [relevant topic]

============================================================
```

**After round 5:**

```
============================================================
CLAWBACK TRAINING — RESULTS
============================================================

SCORE: [X] / 5

[If 5/5]: Perfect. You caught every one.
[If 3-4/5]: Solid instincts. Review the rounds you missed.
[If 0-2/5]: These techniques work on smart people — that's
why scammers use them. Knowing the playbook is the first
defence.

------------------------------------------------------------
YOUR WEAK SPOTS
------------------------------------------------------------
[Based on missed rounds, identify which Cialdini principles
 or attack vectors they're most vulnerable to.]

Recommended reading:
→ /clawback-learn [topic matching weak spot]

------------------------------------------------------------
WHAT TO DO NEXT
------------------------------------------------------------
- Run /clawback-train setup to harden your exchange security
- Run /clawback-train personal for training based on your wallet
- Run /clawback-train quiz again — different scenarios each time

============================================================
```

### Scenario Library (14 Scenarios)

Each scenario includes: content, answer, Cialdini principles, attack vector, the tell, difficulty, and related Learn topic. Learn topics link to `/clawback-learn [topic]`.

**Scenario 1** — Fake Exchange Support WhatsApp

| Field | Value |
|---|---|
| Content | "You receive a WhatsApp message: 'Dear Valued User, This is the Binance Security Department. We have detected a suspicious login from an unrecognised device in Vietnam. Your account withdrawals have been temporarily frozen for your protection. Please verify your identity immediately to restore access: [link]. Failure to verify within 6 hours will result in permanent account suspension. — Binance Security Team'" |
| Answer | SCAM |
| Cialdini | Authority, Scarcity |
| Attack Vector | Fake Exchange Support |
| The Tell | Binance does not contact users via WhatsApp. Official communications happen in-app or via verified email. Any "support" message on WhatsApp is fraudulent by default. |
| Difficulty | 1 |
| Learn Topic | fake exchange support |

**Scenario 2** — Pig Butchering Opener

| Field | Value |
|---|---|
| Content | "You match with someone on Bumble. After 2 weeks of daily texting about travel, food, and music, they mention: 'I used to be stressed about money until I got into crypto investing. My portfolio manager uses a system that consistently returns 20-30% monthly. Changed my life honestly. I could introduce you if you're interested — no pressure though!'" |
| Answer | SCAM |
| Cialdini | Liking, Authority, Social Proof |
| Attack Vector | Pig Butchering |
| The Tell | 20-30% monthly returns do not exist in legitimate finance. The casual "no pressure" delivery is deliberate — pig butchering always frames the financial ask as a favour between friends/partners, never a sales pitch. |
| Difficulty | 2 |
| Learn Topic | pig butchering |

**Scenario 3** — Real Withdrawal Confirmation Email

| Field | Value |
|---|---|
| Content | "You receive an email from no-reply@post.binance.com: 'Withdrawal Successful. You have successfully withdrawn 0.25 BTC to address bc1q...7x9f. Transaction ID: [hash]. If you did not initiate this withdrawal, please lock your account immediately via the Binance app.' You did withdraw 0.25 BTC to that address 10 minutes ago. Your anti-phishing code 'TIGER7' is displayed in the email header." |
| Answer | LEGITIMATE |
| Cialdini | N/A |
| Attack Vector | N/A |
| The Tell | Three confirmations: (1) The sender is a known legitimate Binance email address, (2) the withdrawal details match an action you actually performed, (3) your anti-phishing code is present. The email confirms an action YOU initiated — it doesn't ask you to do anything new. |
| Difficulty | 2 |
| Learn Topic | anti-phishing code |

**Scenario 4** — Shared Seed Phrase in Telegram

| Field | Value |
|---|---|
| Content | "In a crypto Telegram group, a user posts: 'Hey everyone, I'm leaving crypto and don't need this wallet anymore. Here's the seed phrase, someone can have what's left in it: [12 words]. There's about $3,000 in USDT and some BNB. First come first served. Good luck everyone!'" |
| Answer | SCAM |
| Cialdini | Reciprocity, Scarcity |
| Attack Vector | Shared Seed Phrase Scam |
| The Tell | Nobody gives away $3,000 to strangers. The wallet likely has tokens but no gas — when you send gas to withdraw, an automated bot sweeps it instantly. |
| Difficulty | 1 |
| Learn Topic | shared seed phrase scam |

**Scenario 5** — Fake Airdrop Claim

| Field | Value |
|---|---|
| Content | "You notice a new token called 'ARB Rewards' in your Ethereum wallet that you never purchased. A Twitter post from @ARBRewardsOfficial says: 'Eligible wallets have been airdropped ARB Rewards tokens! Claim your bonus allocation (worth up to $500) at arbrewards-claim.io before the claim window closes in 48 hours. Over 25,000 wallets have already claimed.'" |
| Answer | SCAM |
| Cialdini | Scarcity, Social Proof, Reciprocity |
| Attack Vector | Fake Airdrop / Wallet Drainer |
| The Tell | Tokens you didn't buy + a "claim" site = wallet drainer setup. The 48-hour window and "25,000 wallets already claimed" are manufactured urgency and social proof. Legitimate airdrops don't require you to visit third-party claim sites. |
| Difficulty | 2 |
| Learn Topic | fake airdrop / wallet drainer |

**Scenario 6** — Recurring Buy Confirmation

| Field | Value |
|---|---|
| Content | "You receive an email from no-reply@coinbase.com: 'Your recurring buy of $100 of Bitcoin was completed successfully. Transaction details: $100.00 USD → 0.00145 BTC. View your balance in the Coinbase app.' You set up a recurring weekly BTC buy on Coinbase last month. The amount and the email address match. There are no links to click — just a confirmation of the purchase." |
| Answer | LEGITIMATE |
| Cialdini | N/A |
| Attack Vector | N/A |
| The Tell | Sender is the known legitimate Coinbase email address, the transaction matches a recurring buy you actually set up, and the email is a notification that doesn't ask you to do anything. No links, no urgency, no credentials requested. It confirms an action that was already automated — it doesn't ask you to take a new one. |
| Difficulty | 2 |
| Learn Topic | phishing |

**Scenario 7** — Recovery Scam

| Field | Value |
|---|---|
| Content | "After posting on Reddit about losing $15,000 in a rug pull, you receive a DM: 'Hey, I saw your post. I lost $40K last year in a similar scam. I found this company called CryptoRecovery Pro that actually got 80% of my funds back. They use blockchain forensic analysis. Here's their website. It took about 3 weeks and they charged a 15% recovery fee upfront, but I got most of my money back. Just wanted to help since I know how it feels.'" |
| Answer | SCAM |
| Cialdini | Authority, Reciprocity, Liking |
| Attack Vector | Recovery Scam |
| The Tell | "15% recovery fee upfront" — legitimate recovery goes through law enforcement and does not charge victims upfront. The "fellow victim" framing uses Liking and Unity to lower your guard. The DM was unsolicited. |
| Difficulty | 2 |
| Learn Topic | recovery scam |

**Scenario 8** — Fake Influencer DM

| Field | Value |
|---|---|
| Content | "You receive a DM on Twitter from @CZ_Binance_Official: 'Thank you for being a loyal Binance community member. As part of our 9th anniversary celebration, we are distributing BNB rewards to active users. You have been selected to receive 2.5 BNB. Please visit [link] and connect your wallet to claim. — CZ'" |
| Answer | SCAM |
| Cialdini | Authority, Reciprocity, Liking |
| Attack Vector | Impersonation of Known Figures |
| The Tell | CZ does not DM random users to give away BNB. The handle "@CZ_Binance_Official" is not CZ's real handle. No public figure in crypto distributes funds via DMs. "Connect your wallet to claim" = wallet drainer. |
| Difficulty | 2 |
| Learn Topic | fake airdrop / wallet drainer |

**Scenario 9** — Real P2P Trade Dispute Notification

| Field | Value |
|---|---|
| Content | "Inside the Binance app, in the P2P section, you see a notification: 'Your P2P order #12345678 has been appealed by the counterparty. The buyer claims payment was sent but you have not released the crypto. Please provide proof of non-receipt within 24 hours by uploading your bank statement in the appeal chat. A Binance customer service agent will review the case.' You have an active P2P sell order and the buyer has marked payment as sent, but you haven't checked your bank yet." |
| Answer | LEGITIMATE |
| Cialdini | N/A |
| Attack Vector | N/A |
| The Tell | This appears INSIDE the Binance app in the P2P section — not via WhatsApp, Telegram, or email. It references a real order number for a trade you actually have open. Binance's P2P appeal process works exactly this way. However: verify by navigating to the P2P section yourself rather than tapping a notification, and never provide bank details outside the in-app appeal chat. |
| Difficulty | 3 |
| Learn Topic | fake exchange support |

**Scenario 10** — Fake Staking Platform

| Field | Value |
|---|---|
| Content | "An ad on Twitter reads: 'BNB Cloud Staking — Guaranteed 25% monthly returns. Fully automated. Deposit BNB and earn daily rewards directly to your wallet. Backed by Binance Smart Chain technology. $50M TVL. Over 12,000 stakers. Join now at bnb-cloudstake.io.'" |
| Answer | SCAM |
| Cialdini | Social Proof, Authority |
| Attack Vector | Fake Staking / Yield Scam |
| The Tell | "Guaranteed 25% monthly" — 25% monthly = 1,355% APY. No legitimate protocol in existence guarantees this. "Backed by Binance Smart Chain technology" means nothing — anyone can deploy on BSC. The fake TVL and staker count are manufactured social proof. |
| Difficulty | 1 |
| Learn Topic | fake staking / yield scam |

**Scenario 11** — Legitimate Token Migration Notice

| Field | Value |
|---|---|
| Content | "In a Discord server you've been a member of for 8 months, the project team posts in #announcements (a channel only admins can post in): 'Token Migration: As announced in last week's AMA, we are migrating from v1 to v2 token contract. Deadline: March 30. Migration is available through our official dApp at [project's known domain]. If you hold v1 tokens, visit the dApp and use the migration tool. The v1/v2 swap ratio is 1:1. We will not DM you about this — all instructions are here and on our official website only.' You hold the v1 token and attended the AMA where they discussed this." |
| Answer | LEGITIMATE |
| Cialdini | N/A |
| Attack Vector | N/A |
| The Tell | Multiple legitimacy signals: (1) posted in a locked admin-only channel, (2) references a previous AMA you attended, (3) migration goes through the project's known official domain, (4) explicitly warns "we will not DM you about this." However: always verify the URL independently. Even admin accounts can be compromised. Navigate to the project's official website yourself rather than clicking the Discord link. |
| Difficulty | 3 |
| Learn Topic | phishing |

**Scenario 12** — Discord "Limited Mint" from a Friend

| Field | Value |
|---|---|
| Content | "A friend in a Discord server DMs you: 'Hey bro check this out!! This new NFT project is doing a limited mint, only 500 spots. I just minted 3 and the floor is already 2x. Here's the mint link: [suspicious-domain.xyz/mint]. You gotta be quick, there's only like 80 spots left. A bunch of us from the server already got in.' Your friend's account seems normal — same profile pic, same username." |
| Answer | SCAM |
| Cialdini | Unity, Scarcity, Social Proof |
| Attack Vector | Fake Airdrop / Wallet Drainer |
| The Tell | The friend's Discord account is likely compromised — hacked accounts are the #1 distribution method for mint scam links. The "limited spots" urgency, suspicious domain (.xyz), and "a bunch of us already got in" pressure are classic drainer patterns. Verify by messaging your friend on a different platform before clicking anything. |
| Difficulty | 3 |
| Learn Topic | wallet drainer |

**Scenario 13** — Voice Phishing Call

| Field | Value |
|---|---|
| Content | "You receive a phone call. The caller ID shows your exchange's official support number. The caller says: 'This is the Binance fraud prevention team. We've detected an unauthorised withdrawal attempt from your account happening right now. To protect your funds, we need to verify your identity and move your assets to a secure holding wallet. Please stay on the line — do not hang up, as that will delay the security protocol. I'll walk you through the steps.' The caller sounds professional and calm." |
| Answer | SCAM |
| Cialdini | Authority, Scarcity, Commitment |
| Attack Vector | Voice Phishing & Deepfake Impersonation |
| The Tell | "Do not hang up" is the biggest red flag. A legitimate fraud team would tell you to verify independently, not keep you on the line. Also: exchanges do not call you about security issues — they freeze the account and notify you in-app. Caller ID can be spoofed — the number proves nothing. |
| Difficulty | 2 |
| Learn Topic | vishing |

**Scenario 14** — Address Poisoning

| Field | Value |
|---|---|
| Content | "You check your BSC wallet and notice three small incoming transactions of 0.000001 USDT from an address that starts with 0x8f3c and ends with d4e7 — which looks exactly like your friend's wallet that you regularly send money to. You don't remember your friend sending you anything. When you look more carefully, your friend's actual address is 0x8f3c...d4e7 but the sending address in these transactions is 0x8f3c...d4e7 with different characters in the middle section." |
| Answer | SCAM |
| Cialdini | Commitment & Consistency |
| Attack Vector | Address Poisoning |
| The Tell | Unsolicited micro-transactions from an address that closely resembles a known contact = textbook address poisoning. The attacker is planting their lookalike address in your transaction history, hoping you'll copy it next time you send funds to your friend. The address looks right at a glance but the middle characters are different. |
| Difficulty | 3 |
| Learn Topic | address poisoning |

---

## Mode 2: Security Setup

Interactive guided walkthrough of exchange security hardening. 5 steps, each confirmed by the user before moving to the next.

### Step Format

```
============================================================
CLAWBACK SECURITY SETUP — STEP [X] of 5
============================================================

[STEP NAME]

WHAT: [One sentence explanation]

WHY: [Connect to a real attack this step prevents]

HOW:
- Binance: [specific navigation path]
- Coinbase: [equivalent if it exists]
- General: [generic advice for other exchanges]

LEARN MORE: /clawback-learn [relevant term]

Have you set this up?
============================================================
```

### Step 1: Anti-Phishing Code

```
WHAT: Set a custom code that appears in every real email
from your exchange. Fake emails can't include it.

WHY: Phishing emails are the #1 attack vector against exchange
accounts. This turns "is this email real?" from a guessing
game into a simple check: code present = real, code missing
= fake. It stops phishing attacks before they start.

HOW:
- Binance: App → Profile → Security → Anti-Phishing Code
- Coinbase: Settings → Security → Anti-Phishing Code
  (Coinbase calls it the same thing)
- Kraken: Uses PGP-signed emails instead — different mechanism,
  same purpose. See Kraken's security documentation.
- General: Check your exchange's security settings for an
  anti-phishing or email verification code feature.

LEARN MORE: /clawback-learn anti-phishing code
```

### Step 2: 2FA / Passkeys

```
WHAT: Enable two-factor authentication using an authenticator
app or hardware key — NOT SMS.

WHY: If your password is stolen (phishing, data breach, reuse
across sites), 2FA is the only barrier left. SMS 2FA is
vulnerable to SIM swap attacks. An authenticator app generates
codes on your device and cannot be remotely hijacked.

HOW:
- Binance: App → Security → Authenticator App → Follow setup.
  Recommended: Google Authenticator or Authy.
  If you have SMS 2FA enabled, switch to authenticator first,
  then disable SMS.
- Coinbase: Settings → Security → 2-Step Verification →
  Choose Authenticator App or Security Key.
- General: Every major exchange supports authenticator app 2FA.
  Prioritise authenticator app > hardware key > SMS. Never
  rely on SMS as your only 2FA method.

LEARN MORE: /clawback-learn 2fa
```

### Step 3: Withdrawal Address Whitelist

```
WHAT: Restrict withdrawals to only pre-approved addresses.
Even if someone gets full access to your account, they
cannot withdraw to their own address.

WHY: This is your last line of defence. If your password AND
2FA are compromised, the whitelist prevents the attacker
from withdrawing to their address. Most exchanges enforce
a 24-hour cooling period when adding new whitelist addresses,
giving you time to react.

HOW:
- Binance: App → Security → Withdrawal Whitelist → Enable
  → Add your known withdrawal addresses.
- Coinbase: Settings → Whitelisting → Enable address
  whitelisting. 48-hour hold on new addresses.
- General: Most major exchanges support withdrawal address
  whitelisting. The cooling period on new addresses is the
  key protective feature — it means even if an attacker adds
  their address, they must wait 24-48 hours to withdraw.

LEARN MORE: /clawback-learn security
```

### Step 4: Device Management

```
WHAT: Review the list of devices authorised to access your
account. Remove any you don't recognise.

WHY: An attacker who gained access in the past may still have
an authorised session or device. Old phones you sold or gave
away may still be logged in. Each authorised device is a
potential access point.

HOW:
- Binance: App → Security → Device Management → Review list.
  Remove any devices you don't recognise or no longer use.
  Check for unusual locations.
- Coinbase: Settings → Activity → Active Sessions. End any
  sessions you don't recognise.
- General: Check your exchange's security settings for
  "device management," "active sessions," or "login history."
  Remove anything unfamiliar.

LEARN MORE: /clawback-learn 2fa
```

### Step 5: API Key Review

```
WHAT: If you have API keys (for trading bots, portfolio
trackers, or other tools), review their permissions. Delete
unused keys.

WHY: API keys with withdrawal permission are as dangerous as
your password. A compromised trading bot or portfolio tracker
with withdrawal access can drain your account. Most API
use cases only need read or trade permission — never
withdrawal.

HOW:
- Binance: App → API Management → Review each key's
  permissions. Disable withdrawal permission on any key
  that doesn't absolutely need it. Delete keys you no longer
  use.
- Coinbase: Settings → API → Same review.
- General: If you don't use API keys, skip this step.
  If you do: read-only permission where possible, trading
  permission if needed for bots, withdrawal permission
  almost never. IP-restrict keys when supported.

LEARN MORE: /clawback-learn security
```

### Completion Summary

```
============================================================
SECURITY SETUP — COMPLETE
============================================================

COMPLETED: [X] of 5 steps

[Checklist with status per step: completed or skipped]

[If all complete]: Your exchange security is now significantly
hardened. These settings won't prevent every attack, but they
eliminate the most common ones. The biggest remaining risk is
social engineering — no setting protects against willingly
entering your password on a fake site.

[If some skipped]: You skipped [steps]. Each one you skip is
a door left unlocked. Consider completing them when you have
a moment.

NEXT STEPS:
- Run /clawback-train quiz to test your scam detection skills
- Run /clawback-watchdog 0xYourAddress to start wallet monitoring

============================================================
```

---

## Mode 3: Personal Training

Uses the user's actual wallet data from clawback-watchdog to create contextual training scenarios. If watchdog is not active, offer to set it up.

### When Watchdog Data is Available

The skill reads the user's watchdog state (stored in OpenClaw memory) and generates scenarios based on real data:

| Watchdog Data | Training Scenario |
|---|---|
| New airdropped tokens in wallet | "Your wallet was airdropped a token called '[name]' yesterday. What would you check before interacting with it?" |
| Active token approvals (count + unlimited) | "You have [X] active token approvals, [Y] of them unlimited. Walk me through how you'd assess and clean these up." |
| Address retroactively flagged | "An address you interacted with last month just got flagged for phishing. What should you do?" |
| Mix of real data + social engineering | "You received a DM saying your wallet is eligible for a special airdrop claim. You do have new tokens in your wallet. How do you tell if this is real?" |

**Scenario format:**

```
============================================================
CLAWBACK PERSONAL TRAINING
============================================================

Based on your wallet: [truncated address]

SCENARIO:
[Scenario using real data from the user's watchdog state —
 real token names, real approval counts, real addresses]

What would you do?
============================================================
```

**After user responds:**

```
============================================================
ASSESSMENT
============================================================

YOUR RESPONSE: [summary of what they said]

[GOOD / PARTIAL / NEEDS WORK]

------------------------------------------------------------
IDEAL RESPONSE
------------------------------------------------------------
[What the optimal action would be, step by step, referencing
 the specific ClawBack commands and tools]

------------------------------------------------------------
LEARN MORE
------------------------------------------------------------
→ /clawback-learn [relevant topic]
→ [Relevant ClawBack command to take actual action]

============================================================
```

### When Watchdog Data is Not Available

```
Personal Training uses your wallet data to create scenarios
specific to your situation. To enable it:

1. Register your wallet: /clawback-watchdog 0xYourAddress
2. Wait for the initial scan to complete
3. Come back: /clawback-train personal

In the meantime, try:
→ /clawback-learn — build your knowledge base
→ /clawback-train quiz — test your instincts
```

---

## Scoring & Progression

The skill tracks user progress in OpenClaw memory across sessions.

### What is Tracked

| Data | Purpose |
|---|---|
| Quiz scores per session | Track improvement over time |
| Quiz scenarios seen | Avoid repeating the same 5 in consecutive sessions |
| Missed scenarios — Cialdini principles | Identify weak spots |
| Missed scenarios — attack vectors | Identify vulnerable areas |
| Learn topics read | Track coverage |
| Security Setup completion | Track which steps are done |
| Personal Training sessions | Track engagement |

### Progress Display

When a user runs `/clawback-train` with existing history, append below the mode menu:

```
------------------------------------------------------------
YOUR PROGRESS
------------------------------------------------------------
  Learn:    [X] of 30 topics read (via /clawback-learn)
  Quiz:     [X] sessions, best score [X]/5, average [X.X]/5
  Setup:    [X] of 5 steps completed [list missing steps]
  Personal: [status — active / not started / requires watchdog]
  Weak spot: [Cialdini principle or attack vector missed most
             often across quiz sessions]
------------------------------------------------------------
```

### Weak Spot Analysis

After 2+ quiz sessions, the skill identifies patterns in incorrect answers:

- Which Cialdini principles the user fails to detect (e.g. "You missed both scenarios using Authority impersonation")
- Which attack vectors the user misses (e.g. "You haven't caught any recovery scam patterns yet")
- Whether the user over-classifies (calls everything a scam) or under-classifies (misses real scams)

Surfaced in quiz results and in the progress display. Includes a recommendation: "Read up: /clawback-learn [topic matching weak spot]."

### Improvement Tracking

After 3+ sessions, the skill can note trends:

- "Your accuracy has improved from 40% to 80% over 3 sessions."
- "You've been caught by Authority impersonation in 3 of your last 4 sessions — this is your most persistent blind spot."
- "You've aced the quiz but haven't done Security Setup — that's like knowing how to spot a burglar but leaving your door unlocked."

---

## Skill Behaviour

### Tone and Posture

- **Coach, not teacher.** Encouraging but honest. Never condescending about wrong answers — "These techniques work on smart people. That's why scammers use them."
- **Celebrates improvement, not perfection.** Going from 2/5 to 4/5 is a win.
- **Names the technique every time.** After 5 quizzes, the user should be able to name Cialdini principles themselves.
- **Cross-link everything.** Every quiz explanation links to a Learn topic via `/clawback-learn`. Every Setup step links to the concept it protects against.

### Interactive Pacing

CRITICAL: Present ONE quiz round at a time, then STOP and WAIT for the user's answer. Do not present multiple rounds in one message. The flow: present round → stop → user answers → show result → present next round → stop. Only present all rounds together if the user explicitly asks.

### Difficulty Scaling

Quiz difficulty follows the escalation pattern: round 1 is always difficulty 1 (confidence builder), rounds 2-4 are difficulty 2, round 5 is difficulty 3 (the hard one). This ensures the user isn't discouraged early and is challenged at the end.

### Scenario Rotation

The 14-scenario library is large enough for multiple unique sessions. The skill tracks which scenarios have been shown and avoids repeating within 2 consecutive sessions. After the full library has been cycled through, it resets but changes the order.

### Cross-Linking Principle

Every piece of content links to at least one other piece of training content AND at least one other ClawBack skill:

- Quiz explanation → links to `/clawback-learn [topic]` → links to ClawBack skill
- Setup step → links to the concept it protects against via `/clawback-learn` → links to the tool that monitors it
- Personal Training → links to the ClawBack skill that handles the real action

This creates a learning loop, not a dead end.

### Edge Cases

- **User asks for answers during quiz**: "I'll reveal the answer after you commit to your choice. The learning happens in the decision, not the reveal."
- **User runs quiz repeatedly**: Rotate scenarios. After full rotation, note: "You've seen all 14 scenarios. Running again will repeat — but your answers may improve. Consider /clawback-train personal for fresh challenges based on your actual wallet."
- **User has no exchange account**: Security Setup can still be informative — frame as "when you set up an exchange account, do these first." Skip steps that require an active account.
- **User wants non-Binance setup guidance**: Each Setup step includes multi-exchange alternatives. If the user specifies their exchange, tailor the navigation paths.
- **User is a complete beginner**: Route to Learn first. "Start here: /clawback-learn security. This will take 10 minutes and covers everything you need to understand before the quiz."
- **User is an experienced trader**: Route to Quiz → Personal Training. "You probably know the basics. Let's see: /clawback-train quiz. If you score 5/5, try /clawback-train personal for challenges based on your actual wallet data."
- **User asks about a term not in the glossary**: "That term isn't in ClawBack's glossary yet, but here's what it means: [LLM-generated explanation]. For full glossary: /clawback-learn terms."

---

## What This Skill Is NOT

- **Not an educational reference** — use `/clawback-learn` for key terms, scam types, security concepts, and threat briefings
- **Not a threat scanner** — use `clawback-analyse` for real suspicious messages, `clawback-token-check` for real tokens, `clawback-url-scan` for real URLs
- **Not a monitoring service** — use `clawback-watchdog` for ongoing wallet monitoring
- **Not a reporting tool** — use `clawback-report` to generate formal incident reports

This skill is the **practice and hardening layer** — it tests your knowledge through quizzes, hardens your exchange security, and creates personalised training from your own wallet data.

---

## Integration with ClawBack Suite

ClawBack Train is the practice layer that makes every other skill more effective.

| Training Content | Trigger Skill | What to Say |
|---|---|---|
| User wants to understand concepts behind a quiz answer | `clawback-learn` | "Want to understand what [term] means? Run `/clawback-learn [term]` for a plain-English explanation." |
| User learns about token security in quiz | `clawback-token-check` | "Ready to check a real token? Run `/clawback-token-check [address]` to see these concepts in action." |
| User learns about phishing/URL in quiz | `clawback-url-scan` | "Want to check a real URL? Run `/clawback-url-scan [url]` to see domain analysis in action." |
| User learns about social engineering in quiz | `clawback-analyse` | "Got a suspicious message? Run `/clawback-analyse` and paste it for a real Cialdini breakdown." |
| User completes Security Setup | `clawback-watchdog` | "Your exchange is hardened. Now protect your wallet: `/clawback-watchdog [your-wallet-address]`." |
| User completes quiz or personal training | `clawback-watchdog` | "Want 24/7 protection? Run `/clawback-watchdog [your-wallet-address]` to monitor your wallet automatically." |
| User needs to file a report about a real incident | `clawback-report` | "If this isn't training — if you've been targeted for real — run `/clawback-report` to generate a formal incident report." |

---

## About the Builder

**Vance Lever** ([@LeverCRO](https://x.com/LeverCRO)) — AI CRO, agentic economy builder, and crypto security researcher.

The training methodology in ClawBack Train is not theoretical. It draws on years of professional experience in social engineering defence — including published research on manipulation techniques, presentations at major security conferences on voice phishing (vishing) attack patterns, and hands-on experience building enterprise-grade social engineering simulation and training platforms.

The Cialdini framework, the scam playbooks, the voice phishing detection patterns, and the "name the weapon to break the spell" philosophy come from studying, simulating, and defending against these attacks in the real world — not from reading about them.

ClawBack is built on the principle that the best defence against social engineering is understanding exactly how it works — naming the weapons, mapping the playbook, and breaking the emotional spell that makes intelligent people hand over their keys.
