---
name: clawback-learn
description: Crypto security education — key terms, scam types, and security concepts explained in plain English. Covers 30 essential terms (honeypots, rug pulls, seed phrases, token approvals, deepfakes, address poisoning, and more), 12 scam type playbooks with Cialdini psychological analysis, and a four-part security fundamentals guide. Zero API dependencies. Activates via /clawback-learn.
metadata: {"clawdbot":{"emoji":"📖","homepage":"https://clawback.bot"}}
---

# ClawBack Learn

**Crypto security education — key terms, scam types, and security concepts explained in plain English.**

Built by Vance Lever ([@LeverCRO](https://x.com/LeverCRO)) — AI CRO, agentic economy builder, crypto security researcher. Part of the [ClawBack](https://github.com/LeverCRO/clawback) skill suite for the Binance OpenClaw AI Innovation Competition.

Zero API dependencies — pure LLM reasoning and education. This skill is the reference library that makes every other ClawBack skill more effective.

---

## When This Skill Activates

This skill activates **explicitly only** — when a user invokes `/clawback-learn` or any subcommand.

### Commands

| Command | Action |
|---|---|
| `/clawback-learn` | Show topic menu |
| `/clawback-learn terms` | Key terms glossary |
| `/clawback-learn scams` | Scam encyclopedia |
| `/clawback-learn security` | How crypto security works |
| `/clawback-learn [topic]` | Jump to specific term or scam (e.g. `learn honeypot`, `learn pig butchering`) |
| `/clawback-learn briefing` | Threat Briefing — current landscape overview |

---

## Topic Menu

When a user runs `/clawback-learn` with no arguments:

```
============================================================
CLAWBACK LEARN
============================================================

Choose a topic:

KEY TERMS — 30 terms explained in plain English
   → /clawback-learn terms

KNOW YOUR SCAMS — 12 scam types, step by step
   → /clawback-learn scams

HOW CRYPTO SECURITY WORKS — The four layers of defence
   → /clawback-learn security

THREAT BRIEFING — Current crypto scam landscape
   → /clawback-learn briefing

Or jump to any topic:
   /clawback-learn honeypot
   /clawback-learn pig butchering
   /clawback-learn seed phrase

Ready to test your knowledge?
   → /clawback-train quiz

============================================================
```

---

## Key Terms Glossary

A glossary of terms that appear in ClawBack alerts and reports. Each term gets a plain-English definition, why it matters, and a practical takeaway.

**Presentation rule**: When the user asks for the full glossary (`/clawback-learn terms`), present 5-6 terms at a time and ask if they want to continue. When they ask for a specific term (`/clawback-learn honeypot`), show just that term.

**Format per term:**

```
------------------------------------------------------------
[TERM NAME]
------------------------------------------------------------
WHAT IT IS: [Plain English definition — no jargon in the
definition of jargon]

WHY IT MATTERS: [Connect to real danger — what happens if
you don't understand this]

IN THE WILD: [A realistic scenario where this term comes up,
followed by the practical action to take. Include relevant
ClawBack command.]

RELATED: [Other glossary terms, comma-separated]
------------------------------------------------------------
```

**Full term library (30 terms):**

#### Wallet Security

```
------------------------------------------------------------
SEED PHRASE / RECOVERY PHRASE
------------------------------------------------------------
WHAT IT IS: A set of 12 or 24 words generated when you create
a crypto wallet. These words ARE your wallet — anyone who has
them controls every asset in it, on every chain, forever.

WHY IT MATTERS: Your seed phrase is the master key. There is
no "forgot password" flow. If someone else gets it, they own
everything. If you lose it, you lose everything. There is no
recovery service, no support ticket, no exception.

IN THE WILD: "Please enter your 12-word recovery phrase to
verify your wallet." → No legitimate service, exchange, app,
or support agent will EVER ask for your seed phrase. This is
the single most reliable scam indicator in all of crypto. If
anyone asks for it, they are stealing your wallet.
Run /clawback-analyse on any message that mentions seed phrases.

RELATED: private key, wallet drainer, phishing
------------------------------------------------------------
```

```
------------------------------------------------------------
PRIVATE KEY
------------------------------------------------------------
WHAT IT IS: A long string of characters (usually starting with
0x on EVM chains) that proves you own a specific wallet address.
Your seed phrase generates all your private keys. One seed phrase
= many private keys = many wallet addresses.

WHY IT MATTERS: Like a seed phrase but for a single address.
Anyone with your private key can sign transactions as you —
meaning they can send your funds anywhere.

IN THE WILD: "Export your private key and paste it here to
connect your wallet." → Legitimate wallet connections (like
connecting to a DEX) NEVER require your private key. They use
a signing mechanism that proves ownership without exposing
the key. If a site asks you to paste your private key, it is
stealing your wallet.
Run /clawback-url-scan on any site requesting private keys.

RELATED: seed phrase, wallet drainer, smart contract
------------------------------------------------------------
```

#### Account Security

```
------------------------------------------------------------
2FA / TWO-FACTOR AUTHENTICATION
------------------------------------------------------------
WHAT IT IS: A second layer of login security beyond your
password. After entering your password, you must also enter
a code from an authenticator app, a hardware key, or (less
securely) an SMS message.

WHY IT MATTERS: If your password is stolen (phishing, data
breach, reuse), 2FA is the only thing between the attacker
and your account. Without it, a stolen password = full access.

IN THE WILD: "We need your 2FA code to verify your identity."
→ Real exchanges ask for 2FA during login and withdrawals.
Scammers ask for it over WhatsApp, Telegram, email, or phone.
The difference: real 2FA is entered on the platform's own
login page, never shared with a person or pasted into a chat.
Run /clawback-learn sim swap to understand why SMS 2FA
is the weakest option.

RELATED: anti-phishing code, SIM swap, phishing
------------------------------------------------------------
```

```
------------------------------------------------------------
ANTI-PHISHING CODE
------------------------------------------------------------
WHAT IT IS: A custom word or code you set in your exchange
account settings. Every real email from the exchange will
include this code. Phishing emails cannot include it because
the scammer does not know what you set.

WHY IT MATTERS: It turns "is this email real?" from a guessing
game into a simple check. No code = not from the exchange.

IN THE WILD: You receive an urgent email from "Binance" about
a security issue. You check for your anti-phishing code. It's
not there. The email is fake. → Set yours up now:
Binance App → Security → Anti-Phishing Code.
Run /clawback-train setup to walk through this and other
security settings.

RELATED: 2FA, phishing
------------------------------------------------------------
```

#### On-Chain Risk

```
------------------------------------------------------------
TOKEN APPROVAL
------------------------------------------------------------
WHAT IT IS: Permission you grant to a smart contract to spend
a specific token from your wallet. When you trade on a DEX,
you "approve" the DEX contract to move your tokens. This
permission persists until you explicitly revoke it.

WHY IT MATTERS: Approvals don't expire. A contract you approved
6 months ago still has permission to spend your tokens today.
If that contract gets exploited or was malicious from the start,
your approved tokens are at risk — without any further action
from you.

IN THE WILD: You traded on a DEX once in January. In March,
ClawBack Watchdog alerts you that the contract you approved has
been flagged as malicious. Your tokens are at risk even though
you haven't used the DEX in months. → Check and revoke your
approvals regularly. Run /clawback-token-check [your-wallet]
to scan for risky approvals.

RELATED: unlimited approval, revoke, smart contract, wallet drainer
------------------------------------------------------------
```

```
------------------------------------------------------------
UNLIMITED APPROVAL
------------------------------------------------------------
WHAT IT IS: A token approval with no spending cap. Instead of
approving a contract to spend "10 USDT for this trade," you
approve it to spend "unlimited USDT forever." Most DEXs request
unlimited approval by default for convenience.

WHY IT MATTERS: Convenient but dangerous. If the contract is
compromised, the attacker can drain your entire token balance
in that token — not just the amount you traded.

IN THE WILD: You approved unlimited USDT on a DEX. The DEX
gets hacked. The attacker uses your unlimited approval to
transfer all your USDT to themselves. You didn't click anything
— the approval was enough. → Prefer limited approvals when
possible. Revoke unlimited ones you're not actively using.
Run /clawback-token-check [your-wallet] to find unlimited
approvals.

RELATED: token approval, revoke, smart contract
------------------------------------------------------------
```

```
------------------------------------------------------------
SMART CONTRACT
------------------------------------------------------------
WHAT IT IS: A program that lives on the blockchain and executes
automatically when its conditions are met. Every token, DEX,
lending protocol, and DApp is a smart contract. When you
"interact" with DeFi, you are calling functions on smart
contracts.

WHY IT MATTERS: The code IS the contract. There is no customer
service, no dispute resolution, no refund mechanism. If the
code says "take all your tokens," that is what happens. A
"verified" contract means the source code is visible — it does
NOT mean the code is safe.

IN THE WILD: "This contract is verified on BSCScan!" → Verified
means you can read the code. It does not mean the code has been
audited or is safe. Many scam contracts are verified — they want
you to see that they look legitimate. The honeypot logic is
often hidden in complex functions most people can't read.
Run /clawback-token-check [contract-address] to check for
known dangerous patterns.

RELATED: token approval, honeypot, proxy contract, ownership renounced
------------------------------------------------------------
```

```
------------------------------------------------------------
HONEYPOT (TOKEN)
------------------------------------------------------------
WHAT IT IS: A token designed so you can buy it but never sell
it. The smart contract allows buy transactions but blocks,
fails, or taxes 100% of sell transactions.

WHY IT MATTERS: If you buy a honeypot token, your money is
gone permanently. There is no workaround, no support ticket,
no recovery.

IN THE WILD: "Someone in Telegram told me about a token that's
up 500% today." → Before buying ANY token someone recommends,
run /clawback-token-check [address] to check for honeypot
mechanics.

RELATED: rug pull, liquidity lock, mint function
------------------------------------------------------------
```

```
------------------------------------------------------------
RUG PULL
------------------------------------------------------------
WHAT IT IS: When a token creator removes all liquidity from
the trading pool, crashing the price to zero. You can still
sell — but your tokens are now worth nothing.

WHY IT MATTERS: Unlike a honeypot (where you can't sell), a
rug pull lets you sell but there's nothing left to sell for.
The liquidity that gave your tokens value is gone.

IN THE WILD: A new token launches, price goes up for 2 days
as early buyers pile in. On day 3, the deployer pulls the
liquidity pool. Price goes to zero instantly. → Check if
liquidity is locked before buying any new token.
Run /clawback-token-check [address] to check liquidity lock
status.

RELATED: honeypot, liquidity, liquidity lock
------------------------------------------------------------
```

```
------------------------------------------------------------
LIQUIDITY / LIQUIDITY POOL
------------------------------------------------------------
WHAT IT IS: A pool of paired tokens (e.g. BNB + a new token)
locked in a smart contract that enables trading on a DEX. When
you buy or sell a token, you trade against this pool — not
against another person.

WHY IT MATTERS: No liquidity = no ability to trade. The size
of the liquidity pool determines how easily you can buy or
sell and how much your trade moves the price.

IN THE WILD: "This token has $2 million in liquidity!" → Check
whether that liquidity is locked. $2M in unlocked liquidity
means the deployer can withdraw it all at any moment (rug pull).
Run /clawback-token-check [address] to check liquidity depth
and lock status.

RELATED: liquidity lock, rug pull, DEX
------------------------------------------------------------
```

```
------------------------------------------------------------
LIQUIDITY LOCK
------------------------------------------------------------
WHAT IT IS: When liquidity pool tokens are sent to a time-lock
contract that prevents withdrawal until a specific date. This
means the deployer cannot pull liquidity (rug pull) until the
lock expires.

WHY IT MATTERS: A locked liquidity pool is one of the strongest
signals that a token deployer is committed — they literally
cannot rug pull. An unlocked pool means they can exit at any
moment.

IN THE WILD: "Liquidity locked for 6 months!" → Verify this
claim. Check the actual lock contract and expiry date, not just
the deployer's word. Some "locks" have backdoor withdrawal
functions. Run /clawback-token-check [address] for verified
lock status.

RELATED: liquidity, rug pull, ownership renounced
------------------------------------------------------------
```

```
------------------------------------------------------------
MINT FUNCTION
------------------------------------------------------------
WHAT IT IS: A function in a token's smart contract that allows
the creation of new tokens. If the deployer has a mint function,
they can create unlimited new tokens at any time, diluting
every existing holder's share to zero.

WHY IT MATTERS: Even if you can sell and liquidity is locked,
a hidden mint function lets the deployer inflate the supply
and dump millions of new tokens into the market, crashing
the price.

IN THE WILD: "Tokenomics: fixed supply of 1 billion tokens."
→ The claim doesn't matter if the contract has a mint function.
Run /clawback-token-check [address] — it checks for mint
functions regardless of what the project claims.

RELATED: honeypot, ownership renounced, smart contract
------------------------------------------------------------
```

```
------------------------------------------------------------
OWNERSHIP RENOUNCED
------------------------------------------------------------
WHAT IT IS: When a token deployer permanently gives up admin
control over the smart contract. After renouncing, no one can
change taxes, mint tokens, blacklist wallets, or modify the
contract rules.

WHY IT MATTERS: Retained ownership means the deployer can
change the rules at any time — set sell tax to 100%, mint
new tokens, blacklist your wallet. Renounced ownership removes
this risk.

IN THE WILD: "Ownership renounced! Community-driven token!"
→ Verify it on-chain. Some contracts have functions that allow
ownership to be reclaimed after renouncing. Run
/clawback-token-check [address] — it checks for both
renunciation status AND reclamation backdoors.

RELATED: mint function, smart contract, proxy contract
------------------------------------------------------------
```

```
------------------------------------------------------------
PROXY / UPGRADEABLE CONTRACT
------------------------------------------------------------
WHAT IT IS: A smart contract design where the contract you
interact with is a "shell" that forwards calls to a separate
implementation contract. The owner can swap the implementation,
changing how the contract behaves without deploying a new one.

WHY IT MATTERS: A proxy contract that is safe today can become
malicious tomorrow. The owner can upgrade the logic to add
honeypot mechanics, mint functions, or drain functions — and
your token approvals still point at the same proxy address.

IN THE WILD: "Contract audited and verified!" → Was the audit
before or after the last upgrade? A proxy contract can be
upgraded at any time. Run /clawback-token-check [address] to
check for proxy patterns and ownership status.

RELATED: smart contract, ownership renounced, token approval
------------------------------------------------------------
```

```
------------------------------------------------------------
ADDRESS POISONING
------------------------------------------------------------
WHAT IT IS: An attack where scammers send tiny (often zero-value)
transactions to your wallet FROM an address that looks almost
identical to one you regularly transact with — same first 4 and
last 4 characters, different middle. The goal: when you later
copy an address from your transaction history, you accidentally
copy the attacker's lookalike address instead of the real one.

WHY IT MATTERS: This doesn't require phishing, social engineering,
or contract interaction. It exploits the common habit of copying
addresses from transaction history rather than from your contacts
or address book. One wrong paste and your funds go to the attacker.

IN THE WILD: You regularly send USDT to your friend's wallet
0xAbcd...9876. A zero-value transaction appears in your history
from 0xAbcd...9876 — looks like the same address. Next time you
send, you copy from history. But the middle characters are
different — it's the attacker's address. Your USDT is gone.
→ Never copy addresses from transaction history. Use a saved
address book or contacts list. Always verify the FULL address,
not just the first and last few characters.

RELATED: wallet drainer, block explorer
------------------------------------------------------------
```

#### Trading Fundamentals

```
------------------------------------------------------------
DEX (DECENTRALISED EXCHANGE)
------------------------------------------------------------
WHAT IT IS: A trading platform that runs entirely on smart
contracts — no company, no support team, no KYC. You connect
your wallet and trade directly against liquidity pools.
PancakeSwap (BSC), Uniswap (Ethereum), and Raydium (Solana)
are major DEXs.

WHY IT MATTERS: Anyone can list a token on a DEX. There is
no vetting, no listing requirements, no consumer protection.
The token you're buying might be a honeypot, and the DEX
won't stop you — it can't. The code executes.

IN THE WILD: "Just launched on PancakeSwap! Buy now before
it moons!" → Being listed on a DEX means nothing about token
quality. Run /clawback-token-check [address] before buying
anything on a DEX.

RELATED: liquidity, smart contract, token approval
------------------------------------------------------------
```

```
------------------------------------------------------------
GAS FEES
------------------------------------------------------------
WHAT IT IS: The cost of executing a transaction on a
blockchain. Every action — sending tokens, swapping, approving
a contract — costs a small fee paid in the chain's native
token (BNB on BSC, ETH on Ethereum).

WHY IT MATTERS: Gas fees are how scammers bait you in the
shared seed phrase scam — they show you a wallet with tokens
but no gas. You send gas (real money) to "unlock" the tokens.
The scammer's bot sweeps your gas instantly.

IN THE WILD: "I found a wallet with $50,000 in tokens but it
needs $100 in BNB for gas to withdraw!" → This is the shared
seed phrase scam. The wallet is a trap. Your gas will be
stolen by an automated bot. Run /clawback-analyse on any
message sharing a "free" wallet.

RELATED: seed phrase, smart contract
------------------------------------------------------------
```

#### Social Engineering

```
------------------------------------------------------------
PHISHING
------------------------------------------------------------
WHAT IT IS: Creating a fake version of a legitimate website,
email, or message to trick you into entering your credentials.
The fake site looks identical to the real one — same logo,
same layout, same login form. But the data you enter goes
to the attacker, not the real platform.

WHY IT MATTERS: Phishing doesn't "hack" your account — it
tricks YOU into handing over the keys. No amount of security
technology protects against willingly entering your password
on a fake site.

IN THE WILD: You receive an email from "Binance" about a
security issue with a link to "verify your account." The
link goes to binance-secure-verify.com instead of
binance.com. The login page looks perfect. → Check the URL
before entering anything. Run /clawback-url-scan [url] to
verify the domain.

RELATED: social engineering, anti-phishing code, 2FA
------------------------------------------------------------
```

```
------------------------------------------------------------
SOCIAL ENGINEERING
------------------------------------------------------------
WHAT IT IS: Manipulating people into performing actions or
revealing information by exploiting psychological principles
— authority, urgency, trust, reciprocity. The attack targets
the human, not the technology.

WHY IT MATTERS: Every crypto scam at its core is social
engineering. The technology (phishing sites, honeypot
contracts, fake platforms) is just the delivery mechanism.
The manipulation is what makes it work.

IN THE WILD: "This is the Binance Risk Control Department.
Your account has been flagged." → The technology is a simple
WhatsApp message. The attack is Authority (fake official) +
Scarcity (urgency). Understanding the psychology is the best
defence. Run /clawback-analyse on any suspicious message for
full Cialdini breakdown.

RELATED: phishing, pig butchering
------------------------------------------------------------
```

```
------------------------------------------------------------
PIG BUTCHERING
------------------------------------------------------------
WHAT IT IS: A long-con scam where the attacker builds a
genuine emotional connection (romantic or friendship) over
weeks or months before introducing a fake investment platform.
Named because the victim is "fattened up" with trust before
being "slaughtered" financially.

WHY IT MATTERS: The most financially devastating crypto scam.
Average victim losses range from $50,000 to $200,000. Victims
are often educated, financially literate professionals —
the emotional manipulation bypasses rational thinking.

IN THE WILD: Someone you met on a dating app 3 weeks ago
casually mentions they've been earning 30% monthly on crypto.
They offer to show you how. → Dating app + crypto success +
platform recommendation = the three-part pig butchering
pattern. Every time. Run /clawback-analyse on their messages.

RELATED: social engineering, phishing
------------------------------------------------------------
```

#### Scam Mechanics

```
------------------------------------------------------------
AIRDROP
------------------------------------------------------------
WHAT IT IS: Tokens distributed to wallets for free — sometimes
legitimately (project marketing, governance token distribution)
and sometimes maliciously (honeypot lures, wallet drainer bait).

WHY IT MATTERS: Legitimate airdrops exist and can be valuable.
But scam airdrops are designed to trick you into interacting
with a malicious contract. The act of trying to "claim" or
"sell" a scam airdrop is what steals your money.

IN THE WILD: Tokens you never bought appear in your wallet.
A website promises you can "claim" more by connecting your
wallet. → If you didn't buy it, don't touch it. Run
/clawback-token-check [token-address] to check if it's
a honeypot before doing anything.

RELATED: honeypot, wallet drainer, token approval
------------------------------------------------------------
```

```
------------------------------------------------------------
WALLET DRAINER
------------------------------------------------------------
WHAT IT IS: A malicious smart contract or website designed to
trick you into signing a transaction that transfers your assets
or grants unlimited token approvals to the attacker. Often
disguised as an NFT mint, airdrop claim, or DApp connection.

WHY IT MATTERS: A single "approve" click can give the drainer
access to every token of a specific type in your wallet. Some
drainers use permit signatures that don't even require a
separate approval transaction.

IN THE WILD: "Connect your wallet to claim your exclusive
airdrop!" → The "claim" site is a drainer. Connecting your
wallet and signing the transaction grants the drainer full
access to your tokens. Run /clawback-url-scan on any "claim"
site URL before connecting.

RELATED: token approval, unlimited approval, airdrop, phishing
------------------------------------------------------------
```

```
------------------------------------------------------------
SIM SWAP
------------------------------------------------------------
WHAT IT IS: An attack where someone convinces your mobile
carrier to transfer your phone number to a new SIM card they
control. Once they have your number, they receive your SMS
messages — including 2FA codes.

WHY IT MATTERS: If you use SMS-based 2FA, a SIM swap gives
the attacker your second factor. Combined with a phished
password, they have full access to your account.

IN THE WILD: Your phone suddenly loses signal. You can't make
calls. Meanwhile, the attacker is logging into your exchange
account with your password (phished earlier) and the 2FA code
sent to your now-stolen phone number. → Switch from SMS 2FA
to an authenticator app or hardware key immediately.
Run /clawback-train setup to walk through your 2FA settings.

RELATED: 2FA, phishing
------------------------------------------------------------
```

#### Compliance / Verification

```
------------------------------------------------------------
KYC (KNOW YOUR CUSTOMER)
------------------------------------------------------------
WHAT IT IS: Identity verification required by regulated
exchanges — typically uploading a government ID, taking a
selfie, and providing personal information. Legitimate KYC
happens inside the exchange app or website.

WHY IT MATTERS: Scammers exploit KYC in two ways: (1) fake
"KYC verification" messages that harvest your personal
documents, and (2) using the concept of "compliance" as an
authority lever to pressure you into action.

IN THE WILD: "Your KYC verification has expired. Submit
your documents within 24 hours or your account will be
frozen." → Real KYC requests happen inside the exchange
app, not via email/WhatsApp/Telegram. Check by logging
into the app directly. Run /clawback-analyse on the message.

RELATED: phishing, social engineering
------------------------------------------------------------
```

#### Defence Actions

```
------------------------------------------------------------
REVOKE (APPROVAL)
------------------------------------------------------------
WHAT IT IS: Cancelling a previously granted token approval.
After revoking, the contract can no longer spend your tokens.
This is done through a block explorer's token approval checker
or through specialised revoke tools.

WHY IT MATTERS: Every approval is an open door. Revoking
closes doors you no longer need open. This is your primary
defence against stale approvals and retroactively flagged
contracts.

IN THE WILD: ClawBack Watchdog alerts you that a contract you
approved months ago has been flagged as malicious. → Go to
your chain's approval checker (bscscan.com/tokenapprovalchecker
for BSC) and revoke the approval immediately. Run
/clawback-token-check [your-wallet] to find all your active
approvals.

RELATED: token approval, unlimited approval
------------------------------------------------------------
```

```
------------------------------------------------------------
BLOCK EXPLORER
------------------------------------------------------------
WHAT IT IS: A website that lets you look up any transaction,
address, token, or contract on a blockchain. BSCScan (BSC),
Etherscan (Ethereum), Solscan (Solana) are the major ones.
Think of it as the public ledger viewer.

WHY IT MATTERS: Block explorers are your verification tool.
If someone claims a transaction happened, check the explorer.
If someone gives you a contract address, look it up. Everything
on the blockchain is public and auditable.

IN THE WILD: "We've already sent the tokens to your wallet."
→ Check the block explorer. If the transaction isn't there,
it didn't happen. BSCScan: bscscan.com/address/[your-address].
Run /clawback-token-check [address] to get a security
analysis on top of the raw block explorer data.

RELATED: smart contract, token approval, revoke
------------------------------------------------------------
```

#### Voice & Video Threats

```
------------------------------------------------------------
VISHING (VOICE PHISHING)
------------------------------------------------------------
WHAT IT IS: Phone-based social engineering where the caller
impersonates exchange support, law enforcement, or a bank.
Unlike email phishing, vishing creates real-time pressure —
the caller controls the pace and you can't pause to verify.

WHY IT MATTERS: Voice creates urgency that text cannot.
When someone is talking at you, claiming your funds are
being stolen right now, the instinct to act overwhelms the
instinct to verify. Caller ID spoofing makes the call appear
to come from a real support number.

IN THE WILD: "You receive a call from someone claiming to be
Binance security, saying your account is being drained right
now and you need to transfer funds to a 'safe wallet'
immediately." → Exchanges never call you about security
issues. If someone calls claiming to be support, hang up and
contact support through the official app.
Run /clawback-analyse and describe the call for a full
social engineering breakdown.

RELATED: social engineering, phishing, deepfake (audio),
caller ID spoofing
------------------------------------------------------------
```

```
------------------------------------------------------------
DEEPFAKE (AUDIO)
------------------------------------------------------------
WHAT IT IS: AI-generated voice that sounds identical to a
real person. Scammers clone voices from public videos,
podcasts, or social media to impersonate executives,
influencers, or even people you know personally. A few
seconds of audio is enough to create a convincing clone.

WHY IT MATTERS: Voice used to be proof of identity. "I
spoke to them on the phone" used to end the debate. Now it
means nothing — AI can generate any voice in real time,
complete with natural inflection, emotion, and accent.

IN THE WILD: "Your 'boss' calls asking you to urgently
transfer crypto for a business deal. The voice sounds
exactly like them." → Never act on voice instructions alone
for financial transactions. Verify through a separate
channel — text the person directly, call back on a number
you know, or confirm face to face.

RELATED: vishing, social engineering, deepfake (video)
------------------------------------------------------------
```

```
------------------------------------------------------------
DEEPFAKE (VIDEO)
------------------------------------------------------------
WHAT IT IS: AI-generated real-time video that makes the
caller appear to be someone else on a video call. Now used
in pig butchering operations to pass the "let's video chat"
verification that previously caught scammers. Quality varies
but is improving rapidly — in low-light or compressed video
calls, deepfakes are increasingly hard to detect.

WHY IT MATTERS: "Just video call them" was the standard
advice for verifying someone's identity online. That advice
is now dangerously outdated. Pig butchering operations use
real-time deepfake video to build trust over weeks, making
victims certain they know who they're talking to.

IN THE WILD: "Your Hinge match finally agrees to video call.
They look exactly like their photos. You're now convinced
they're real." → Video calls no longer prove identity. Look
for: unnatural blinking, audio-visual sync issues, reluctance
to turn head or show profile view, lighting artifacts around
face edges. But the technology is outpacing detection —
verify identity through methods that can't be faked: meet in
person, or ask them to do something specific and unpredictable
in real time.

RELATED: pig butchering, vishing, deepfake (audio)
------------------------------------------------------------
```

```
------------------------------------------------------------
CALLER ID SPOOFING
------------------------------------------------------------
WHAT IT IS: Technology that makes a phone call appear to come
from a different number than the actual caller. Scammers can
make calls appear to come from your exchange's real support
number, your bank, or even a government agency.

WHY IT MATTERS: Caller ID was designed for convenience, not
security. It proves nothing about who is actually calling.
When you see your exchange's number on the screen, your
guard drops — which is exactly the point.

IN THE WILD: "Incoming call from +1-800-BINANCE. Must be
real — it's their official number." → Caller ID proves
nothing. Anyone can spoof any number. If you receive an
unexpected call about your account, hang up and call back
using the number from the official website or app.

RELATED: vishing, social engineering
------------------------------------------------------------
```

---

## Know Your Scams — Scam Encyclopedia

An encyclopedia of crypto scam types. When user runs `/clawback-learn scams`, show the list of 12 scam types:

```
============================================================
CLAWBACK LEARN — KNOW YOUR SCAMS
============================================================

Choose a scam type to learn about:

 1. Fake Exchange Support
 2. Pig Butchering (Romance/Long Con)
 3. Honeypot Token
 4. Rug Pull
 5. Phishing Email/Site
 6. Shared Seed Phrase Scam
 7. Fake Airdrop / Wallet Drainer
 8. Recovery Scam
 9. Fake Staking / Yield Scam
10. SIM Swap Attack
11. Voice Phishing & Deepfake Impersonation
12. Address Poisoning

→ /clawback-learn scams [number or name]
→ Or browse sequentially: /clawback-learn scams 1

Ready to test your knowledge? → /clawback-train quiz

============================================================
```

**Format per scam type:**

```
============================================================
SCAM TYPE: [NAME]
============================================================

ALSO KNOWN AS: [alternative names]
RISK LEVEL: [CRITICAL / HIGH / MEDIUM] — [typical loss range]
TIME TO EXECUTE: [seconds to months]

------------------------------------------------------------
HOW IT WORKS
------------------------------------------------------------
[Step-by-step playbook, numbered. Plain English. 4-7 steps.]

------------------------------------------------------------
PSYCHOLOGICAL WEAPONS (Cialdini)
------------------------------------------------------------
[Which Cialdini principles are active, with one-line explanation
 of how each is applied in this scam type.]

------------------------------------------------------------
HOW TO SPOT IT
------------------------------------------------------------
[3-5 specific indicators. Practical, not theoretical.]

------------------------------------------------------------
IF YOU'RE TARGETED
------------------------------------------------------------
[What to do RIGHT NOW. Specific actions, not generic advice.
 Include relevant ClawBack commands.]

============================================================
```

**Full scam library (12 entries):**

#### 1. Fake Exchange Support

```
============================================================
SCAM TYPE: FAKE EXCHANGE SUPPORT
============================================================

ALSO KNOWN AS: Support impersonation, customer service scam
RISK LEVEL: CRITICAL — full account compromise, total fund loss
TIME TO EXECUTE: Minutes to hours

------------------------------------------------------------
HOW IT WORKS
------------------------------------------------------------
Step 1: You receive an unsolicited message via WhatsApp,
  Telegram, email, phone call, or social media DM claiming
  to be from exchange support.
Step 2: They reference a fake security issue — "unusual
  activity detected," "account under review," "withdrawal
  suspended."
Step 3: They create urgency — act now or lose access.
Step 4: They direct you to a phishing site or ask you to
  share credentials, 2FA codes, or seed phrase directly.
Step 5: They access your account and drain funds, often
  within minutes.

------------------------------------------------------------
PSYCHOLOGICAL WEAPONS (Cialdini)
------------------------------------------------------------
- Authority: Impersonating official support staff
- Scarcity: "Your account will be locked in 12 hours"
- Reciprocity: "We've secured your funds" (fake help)

------------------------------------------------------------
HOW TO SPOT IT
------------------------------------------------------------
- Unsolicited contact on unofficial channels (WhatsApp,
  Telegram DMs, phone calls). Major exchanges do not
  initiate contact this way.
- Requests for passwords, 2FA codes, or seed phrases.
  No legitimate support agent will ever ask for these.
- Urgency that prevents you from pausing to verify.
- Links to domains that are not the exchange's official domain.

------------------------------------------------------------
IF YOU'RE TARGETED
------------------------------------------------------------
- Do not respond. Do not click links.
- Block and report the contact.
- If you shared credentials: change password and reset 2FA
  immediately via the official app.
- Run /clawback-analyse on the message for full breakdown.
- Run /clawback-url-scan on any URLs they sent.

============================================================
```

#### 2. Pig Butchering

```
============================================================
SCAM TYPE: PIG BUTCHERING
============================================================

ALSO KNOWN AS: Romance scam, long con, sha zhu pan
RISK LEVEL: CRITICAL — average loss $50,000-$200,000
TIME TO EXECUTE: Weeks to months

------------------------------------------------------------
HOW IT WORKS
------------------------------------------------------------
Step 1: Contact — They find you on a dating app, LinkedIn, or
  social media. Profile uses stolen photos of an attractive,
  successful professional.
Step 2: Rapport — 2-6 weeks of daily conversation. Building
  genuine emotional connection. They never mention money early.
Step 3: The Bridge — They casually mention their own crypto
  success. "I've been making 30% monthly on this platform."
  They offer to show you — framed as sharing, not selling.
Step 4: Small Deposit — You create an account on their
  platform (which they control). You deposit $500-$2,000.
  The dashboard shows impressive gains within days.
Step 5: Escalation — Encouraged by fake profits, you deposit
  more. Some victims invest life savings over weeks.
Step 6: The Trap — When you try to withdraw, you're told to
  pay a "tax" or "fee" first. This is a second extraction.
Step 7: Disappearance — The platform goes offline. The person
  vanishes. Funds are unrecoverable.

------------------------------------------------------------
PSYCHOLOGICAL WEAPONS (Cialdini)
------------------------------------------------------------
- Liking: Romantic/emotional connection is the delivery vehicle
- Authority: Claims expertise (crypto analyst, finance pro)
- Social Proof: Their own "success" is the testimonial
- Commitment: Small deposit leads to larger (foot-in-door)

------------------------------------------------------------
HOW TO SPOT IT
------------------------------------------------------------
- Met online + mentions crypto success + recommends a platform
  = the three-part pattern. Every time.
- 30% monthly returns do not exist in legitimate finance. Ever.
- They won't video call, or the video quality is suspicious.
- Reverse image search their profile photo.
- The "platform" they recommend has no presence on major
  review sites, crypto forums, or social media.

------------------------------------------------------------
IF YOU'RE TARGETED
------------------------------------------------------------
- Stop engaging. Do not confront (rehearsed objections).
- Do NOT deposit more to "unlock" withdrawals.
- Report: local police, ic3.gov (US), Action Fraud (UK).
- Run /clawback-analyse on their messages for full breakdown.

============================================================
```

#### 3. Honeypot Token

```
============================================================
SCAM TYPE: HONEYPOT TOKEN
============================================================

ALSO KNOWN AS: Buy-only token, sell-block token
RISK LEVEL: CRITICAL — 100% loss, unrecoverable
TIME TO EXECUTE: Seconds (the buy transaction IS the loss)

------------------------------------------------------------
HOW IT WORKS
------------------------------------------------------------
Step 1: A new token appears on a DEX, often promoted in
  Telegram groups, Twitter, or Discord as the "next gem."
Step 2: The price chart looks incredible — only green candles,
  steady upward movement.
Step 3: You buy. The transaction succeeds.
Step 4: You try to sell. The transaction fails, reverts, or
  charges 100% tax. Your funds are trapped permanently.
Step 5: The deployer sells their own tokens (the contract
  allows the owner to sell) and withdraws the liquidity.

------------------------------------------------------------
PSYCHOLOGICAL WEAPONS (Cialdini)
------------------------------------------------------------
- Social Proof: "Look at this chart! Everyone's buying!"
- Scarcity: "Get in before it's too late!"
- Authority: Promoted by "influencers" or "alpha callers"

------------------------------------------------------------
HOW TO SPOT IT
------------------------------------------------------------
- Only green candles with no sell pressure = nobody can sell.
- Check the contract before buying. Always.
- Unknown token recommended in a group you recently joined.
- No established community, no real website, no team info.

------------------------------------------------------------
IF YOU'RE TARGETED
------------------------------------------------------------
- If you haven't bought: don't. Run /clawback-token-check
  [address] first.
- If you already bought: your funds in this token are not
  recoverable. Do not send more trying to "fix" it.
- Report the contract on the block explorer.
- Warn others in the group where you found it.

============================================================
```

#### 4. Rug Pull

```
============================================================
SCAM TYPE: RUG PULL
============================================================

ALSO KNOWN AS: LP pull, exit scam
RISK LEVEL: CRITICAL — near-total loss
TIME TO EXECUTE: Hours to days after launch

------------------------------------------------------------
HOW IT WORKS
------------------------------------------------------------
Step 1: Token launches with a liquidity pool on a DEX.
Step 2: Promoted heavily — paid influencers, Telegram raids,
  Twitter shilling. Price pumps.
Step 3: Early buyers see gains. More buyers pile in.
Step 4: Deployer removes the liquidity pool, crashing the
  token price to near zero instantly.
Step 5: Your tokens still exist but are essentially worthless
  — there is no liquidity left to sell into.

------------------------------------------------------------
PSYCHOLOGICAL WEAPONS (Cialdini)
------------------------------------------------------------
- Social Proof: Coordinated shilling creates artificial FOMO
- Scarcity: "Only 24 hours since launch!"
- Commitment: Early gains make you hold instead of sell

------------------------------------------------------------
HOW TO SPOT IT
------------------------------------------------------------
- Liquidity is NOT locked in a time-lock contract.
- Anonymous team with no verifiable history.
- Heavy promotion but no product, no utility, no roadmap.
- Ownership not renounced — deployer retains control.

------------------------------------------------------------
IF YOU'RE TARGETED
------------------------------------------------------------
- Run /clawback-token-check [address] before buying. Check
  liquidity lock status and ownership.
- If you already hold and liquidity hasn't been pulled: sell
  and take whatever you can recover.
- If the rug already happened: your remaining tokens have
  near-zero value. Do not buy more hoping for recovery.

============================================================
```

#### 5. Phishing Email/Site

```
============================================================
SCAM TYPE: PHISHING EMAIL / SITE
============================================================

ALSO KNOWN AS: Credential harvesting, clone site
RISK LEVEL: CRITICAL — full account compromise
TIME TO EXECUTE: Minutes

------------------------------------------------------------
HOW IT WORKS
------------------------------------------------------------
Step 1: You receive an email, text, or ad that looks like
  it's from your exchange — same logo, same tone, same format.
Step 2: It contains a link to "verify your account," "confirm
  a withdrawal," or "update your security."
Step 3: The link goes to a pixel-perfect clone of the exchange
  login page on a lookalike domain.
Step 4: You enter your credentials. They are captured in real
  time by the attacker.
Step 5: The attacker logs into your real account, often
  relaying your 2FA code in real time if prompted.

------------------------------------------------------------
PSYCHOLOGICAL WEAPONS (Cialdini)
------------------------------------------------------------
- Authority: Spoofed sender looks like the real exchange
- Scarcity: "Verify within 24 hours or lose access"
- Commitment: You've used the exchange before, so logging
  in feels routine, not suspicious

------------------------------------------------------------
HOW TO SPOT IT
------------------------------------------------------------
- Check the sender's ACTUAL email address, not the display name.
- Check the URL before entering credentials. Look for typos,
  extra subdomains, non-.com TLDs.
- Missing anti-phishing code in the email.
- Hover over links before clicking — does the URL match?

------------------------------------------------------------
IF YOU'RE TARGETED
------------------------------------------------------------
- Do not click the link. If you already did, do not enter
  credentials.
- Run /clawback-url-scan [url] to check the domain.
- Run /clawback-analyse on the full email for header analysis.
- If you entered credentials: change password and reset 2FA
  NOW from the official app. Check withdrawal addresses.

============================================================
```

#### 6. Shared Seed Phrase Scam

```
============================================================
SCAM TYPE: SHARED SEED PHRASE SCAM
============================================================

ALSO KNOWN AS: Free wallet scam, gas fee trap
RISK LEVEL: HIGH — loss of whatever gas you send
TIME TO EXECUTE: Minutes

------------------------------------------------------------
HOW IT WORKS
------------------------------------------------------------
Step 1: A "generous stranger" in a group or DM shares a seed
  phrase, saying they found a wallet with thousands in tokens.
Step 2: You import the seed phrase. The wallet shows a real
  balance — tokens worth thousands.
Step 3: The tokens need gas fees to transfer out. The wallet
  has no gas.
Step 4: You send gas (real BNB/ETH) to the wallet.
Step 5: An automated bot instantly sweeps your gas to the
  scammer's wallet before you can do anything.

------------------------------------------------------------
PSYCHOLOGICAL WEAPONS (Cialdini)
------------------------------------------------------------
- Reciprocity: "They shared something valuable with me"
- Scarcity: "I need to act fast before someone else takes it"

------------------------------------------------------------
HOW TO SPOT IT
------------------------------------------------------------
- Nobody gives away real money to strangers.
- The tokens in the wallet are often themselves honeypots.
- The wallet has tokens but suspiciously no gas — designed
  to make you provide gas.

------------------------------------------------------------
IF YOU'RE TARGETED
------------------------------------------------------------
- Do not send any gas to the wallet. Close and forget it.
- The tokens are either honeypots or the gas will be stolen.
- Run /clawback-analyse on the message that shared the
  seed phrase.

============================================================
```

#### 7. Fake Airdrop / Wallet Drainer

```
============================================================
SCAM TYPE: FAKE AIRDROP / WALLET DRAINER
============================================================

ALSO KNOWN AS: Claim site scam, approval scam
RISK LEVEL: CRITICAL — can drain entire wallet
TIME TO EXECUTE: Seconds (one transaction)

------------------------------------------------------------
HOW IT WORKS
------------------------------------------------------------
Step 1: Tokens you never bought appear in your wallet.
Step 2: A website or social media post says you can "claim"
  more tokens or "sell" the airdropped ones.
Step 3: The claim site asks you to connect your wallet and
  sign a transaction.
Step 4: The transaction is actually a token approval or
  direct transfer — giving the drainer access to your
  real tokens.
Step 5: The drainer takes everything the approval covers.

------------------------------------------------------------
PSYCHOLOGICAL WEAPONS (Cialdini)
------------------------------------------------------------
- Reciprocity: "Free tokens! Take them!"
- Scarcity: "Claim expires in 48 hours"
- Social Proof: "50,000 wallets already claimed"

------------------------------------------------------------
HOW TO SPOT IT
------------------------------------------------------------
- Tokens you never bought appearing in your wallet.
- Any website asking you to "claim" by signing a transaction.
- The "claim" transaction's details show a suspicious approval
  or transfer, not the airdrop you expected.

------------------------------------------------------------
IF YOU'RE TARGETED
------------------------------------------------------------
- DO NOT interact with unsolicited tokens. Don't sell, don't
  transfer, don't click "claim."
- Run /clawback-token-check [token-address] on any mystery
  token.
- Run /clawback-url-scan on any "claim" site URL.
- If you already connected and signed: revoke approvals
  immediately. Run /clawback-token-check [your-wallet] to
  check for risky approvals.

============================================================
```

#### 8. Recovery Scam

```
============================================================
SCAM TYPE: RECOVERY SCAM
============================================================

ALSO KNOWN AS: Fund recovery fraud, advance fee scam
RISK LEVEL: HIGH — targets already-vulnerable victims
TIME TO EXECUTE: Days to weeks

------------------------------------------------------------
HOW IT WORKS
------------------------------------------------------------
Step 1: You've already been scammed and posted about it
  online, or the original scammers sell your details.
Step 2: Someone contacts you claiming they can recover your
  lost funds — a "recovery expert," "blockchain investigator,"
  or "law firm specialising in crypto fraud."
Step 3: They require an upfront fee — for "legal costs,"
  "blockchain analysis," or "recovery processing."
Step 4: You pay the fee. Nothing is recovered. They may ask
  for additional fees for "complications."
Step 5: You have now been scammed twice.

------------------------------------------------------------
PSYCHOLOGICAL WEAPONS (Cialdini)
------------------------------------------------------------
- Authority: "Certified blockchain forensics expert"
- Reciprocity: "We've already located your funds" (fake help)
- Commitment: After paying one fee, it feels wasteful to stop

------------------------------------------------------------
HOW TO SPOT IT
------------------------------------------------------------
- They contacted YOU about a loss you discussed online.
- Upfront payment required before any work.
- Guaranteed recovery promises — legitimate recovery is
  uncertain, slow, and usually goes through law enforcement.
- No verifiable credentials, company registration, or track
  record.

------------------------------------------------------------
IF YOU'RE TARGETED
------------------------------------------------------------
- Do not pay any upfront fees. Legitimate law enforcement
  does not charge victims.
- Report to local police and ic3.gov (US) or Action Fraud (UK).
- Run /clawback-analyse on their messages.
- Legitimate options: file a police report, report to the
  exchange directly, and consult a licensed attorney.

============================================================
```

#### 9. Fake Staking / Yield Scam

```
============================================================
SCAM TYPE: FAKE STAKING / YIELD SCAM
============================================================

ALSO KNOWN AS: DeFi yield scam, mining pool scam
RISK LEVEL: CRITICAL — total deposit loss
TIME TO EXECUTE: Days to weeks

------------------------------------------------------------
HOW IT WORKS
------------------------------------------------------------
Step 1: A platform promises guaranteed high yields — "25%
  monthly," "earn 2% daily," "guaranteed passive income."
Step 2: You deposit funds. The dashboard shows growing
  returns (all fabricated).
Step 3: Early withdrawals may succeed (funded by new
  deposits — Ponzi mechanics).
Step 4: You deposit more, convinced by the "real" returns.
Step 5: Withdrawals stop. The platform disappears. The
  "yield" was never real.

------------------------------------------------------------
PSYCHOLOGICAL WEAPONS (Cialdini)
------------------------------------------------------------
- Social Proof: Fake testimonials, fabricated user counts
- Authority: Professional-looking dashboard and branding
- Commitment: Early successful withdrawals make you commit more

------------------------------------------------------------
HOW TO SPOT IT
------------------------------------------------------------
- Guaranteed returns in crypto do not exist. Period.
- 25% monthly = 1,355% annually. No legitimate protocol
  sustains this.
- Anonymous team, no audit, not listed on DeFiLlama or
  other DeFi tracking sites.
- Recruitment incentives (referral bonuses) = Ponzi structure.

------------------------------------------------------------
IF YOU'RE TARGETED
------------------------------------------------------------
- Do not deposit more. Withdraw what you can immediately.
- If withdrawals are blocked, do NOT pay "fees" to unlock.
- Run /clawback-url-scan on the platform URL.
- Run /clawback-analyse on any promotional messages.

============================================================
```

#### 10. SIM Swap Attack

```
============================================================
SCAM TYPE: SIM SWAP ATTACK
============================================================

ALSO KNOWN AS: SIM hijacking, number porting scam
RISK LEVEL: CRITICAL — full account takeover
TIME TO EXECUTE: Hours

------------------------------------------------------------
HOW IT WORKS
------------------------------------------------------------
Step 1: The attacker gathers your personal information —
  name, phone number, email, carrier — from social media,
  data breaches, or social engineering your carrier's support.
Step 2: They contact your mobile carrier and convince them
  to port your number to a new SIM card.
Step 3: Your phone loses signal. Their phone now receives
  your calls and texts.
Step 4: They log into your exchange using your password
  (phished earlier or from a data breach).
Step 5: 2FA codes sent via SMS go to THEIR phone. They have
  full access to your account.

------------------------------------------------------------
PSYCHOLOGICAL WEAPONS (Cialdini)
------------------------------------------------------------
- Authority: The attacker impersonates you to your carrier
- This scam is more technical than social — the primary
  manipulation is carrier staff, not the victim

------------------------------------------------------------
HOW TO SPOT IT
------------------------------------------------------------
- Your phone suddenly loses signal for no reason.
- You receive unexpected "account change" notifications from
  your carrier.
- You can't make calls or send texts.
- You notice login notifications from your exchange that
  weren't you.

------------------------------------------------------------
IF YOU'RE TARGETED
------------------------------------------------------------
- Contact your carrier IMMEDIATELY from a different phone.
- Change your exchange password from a device not tied to
  your phone number.
- Switch from SMS 2FA to authenticator app or hardware key.
- Run /clawback-train setup to harden your 2FA settings.

============================================================
```

#### 11. Voice Phishing & Deepfake Impersonation

```
============================================================
SCAM TYPE: VOICE PHISHING & DEEPFAKE IMPERSONATION
============================================================

ALSO KNOWN AS: Vishing, AI voice scam, deepfake call scam
RISK LEVEL: CRITICAL — full account compromise or direct fund transfer
TIME TO EXECUTE: Minutes (single phone call)

------------------------------------------------------------
HOW IT WORKS
------------------------------------------------------------
Step 1: The attacker calls you, spoofing the caller ID to
  show your exchange's real support number or an authority
  figure's number.
Step 2: They create real-time panic — "your account is being
  drained right now" or "there's a compliance issue that needs
  immediate resolution."
Step 3: Unlike text scams, voice creates urgency you can't
  escape — the caller controls the pace. You can't "pause to
  think" when someone is talking at you.
Step 4: They direct you to transfer funds to a "safe wallet,"
  share your screen so they can "help" you, share your 2FA
  code, or click a link they'll send during the call.
Step 5: Advanced versions use AI-cloned voices (deepfake audio)
  to sound like a specific person — your boss, a known
  influencer, or even a family member. Video deepfakes are now
  used in pig butchering to pass video call verification.

------------------------------------------------------------
PSYCHOLOGICAL WEAPONS (Cialdini)
------------------------------------------------------------
- Authority: "This is the fraud department" (caller ID confirms it)
- Scarcity: "Your funds are being stolen RIGHT NOW" (real-time panic)
- Commitment: "Stay on the line while we resolve this" (keeps you
  from hanging up to verify independently)

------------------------------------------------------------
HOW TO SPOT IT
------------------------------------------------------------
- Unsolicited call about your account = suspicious by default.
- Caller asks you to share screen, share 2FA, transfer funds,
  or click a link they send during the call.
- Extreme urgency — "don't hang up, stay on the line."
- Caller ID spoofing makes the number look legitimate — this
  proves nothing.
- For deepfake video: unnatural blinking, audio lag, reluctance
  to show profile view, lighting artifacts around face edges.

------------------------------------------------------------
IF YOU'RE TARGETED
------------------------------------------------------------
- Hang up. This is always the correct first action.
- Call back using the number from the official app or website —
  never the number that called you.
- If the caller claimed to be someone you know: contact that
  person through a different channel to verify.
- Run /clawback-analyse and describe the call for a full
  social engineering breakdown.

============================================================
```

#### 12. Address Poisoning

```
============================================================
SCAM TYPE: ADDRESS POISONING
============================================================

ALSO KNOWN AS: Zero-value transfer attack, dust poisoning,
address spoofing
RISK LEVEL: CRITICAL — full transfer amount lost per incident
TIME TO EXECUTE: Seconds (automated, at scale)

------------------------------------------------------------
HOW IT WORKS
------------------------------------------------------------
Step 1: The attacker generates thousands of wallet addresses
  that match the first and last 4-6 characters of addresses
  you frequently transact with. This is computationally cheap.
Step 2: They send zero-value or dust-amount transactions to
  your wallet from these lookalike addresses. These appear in
  your transaction history.
Step 3: You need to send funds to your regular recipient. You
  open your transaction history and copy what looks like the
  right address.
Step 4: You paste the poisoned address — the first and last
  characters match, so it looks correct at a glance.
Step 5: You send funds to the attacker's address. The
  transaction is irreversible.

------------------------------------------------------------
PSYCHOLOGICAL WEAPONS (Cialdini)
------------------------------------------------------------
- Commitment & Consistency: You've sent to "this address"
  before, so copying from history feels like a safe routine.
- This scam is primarily technical, not social — it exploits
  interface habits rather than emotional manipulation.

------------------------------------------------------------
HOW TO SPOT IT
------------------------------------------------------------
- Unexpected zero-value or tiny transactions in your history
  from addresses you don't recognise.
- Addresses that match the first and last characters of your
  real contacts but differ in the middle.
- Multiple small incoming transactions from slightly different
  addresses — this is a poisoning campaign in progress.

------------------------------------------------------------
IF YOU'RE TARGETED
------------------------------------------------------------
- NEVER copy addresses from transaction history. Use a saved
  contacts list, address book, or the original source.
- Always verify the FULL address character by character before
  confirming any transaction — especially large ones.
- If you sent funds to a poisoned address: the transaction is
  irreversible. Report to the exchange if the receiving address
  is on an exchange, but recovery is unlikely.
- Run /clawback-token-check [poisoned-address] to check the
  attacker's address reputation.

============================================================
```

---

## How Crypto Security Works

When user runs `/clawback-learn security`, present these four sections in order. Written as conversational prose for someone who uses an exchange but doesn't understand the security model underneath.

#### Section 1: Account Security — Your Exchange Login is the Front Door

Your exchange account is protected by credentials — email, password, and (hopefully) 2FA. This is the front door to your funds. If someone gets through it, they can withdraw everything you hold on the exchange.

The most common way attackers get through is phishing — tricking you into entering your password on a fake login page. The fake page looks identical to the real one. Your browser, your antivirus, your operating system won't stop you. The only thing that stops you is recognising that the URL is wrong before you type.

2FA adds a second barrier: even with your password, the attacker needs a code from your phone. But not all 2FA is equal. SMS-based 2FA is vulnerable to SIM swap attacks — where the attacker takes over your phone number and receives your codes. Authenticator apps (Google Authenticator, Authy) generate codes on your device and cannot be remotely hijacked. Hardware keys (YubiKey) are the strongest option — the key must be physically present.

Anti-phishing codes turn every email verification into a simple pass/fail check. You set a custom code in your exchange settings. Every real email from the exchange includes it. Fake emails can't include it because the scammer doesn't know what you set. No code = not from the exchange.

**Practical takeaway:** Use an authenticator app for 2FA, set an anti-phishing code, and bookmark your exchange's login page so you never need to click an email link. Run `/clawback-train setup` to walk through these settings.

#### Section 2: Wallet Security — Your Wallet is a Set of Keys, Not a Bank Account

When you hold crypto in a self-custody wallet (MetaMask, Trust Wallet, a hardware wallet), there is no company behind it. No customer service, no password reset, no fraud department. Your wallet is a pair of cryptographic keys: a public address (where people send you funds) and a private key (which proves you own it). Your seed phrase generates all your private keys.

"Not your keys, not your crypto" means: if your crypto is on an exchange, the exchange holds the keys. They can freeze your account, comply with law enforcement, or get hacked. If you hold your own keys, nobody can freeze or seize your funds — but nobody can recover them either. There is no "forgot password" for a seed phrase.

Hardware wallets (Ledger, Trezor) add physical security — your private key never leaves the device. Even if your computer is compromised, the attacker can't extract the key. You approve transactions by physically pressing a button on the device, making remote theft essentially impossible.

**Practical takeaway:** Never share your seed phrase with anyone, for any reason, ever. Store it offline (written on paper or metal, not in a photo or cloud backup). If you hold significant value, use a hardware wallet. Run `/clawback-analyse` on any message that mentions seed phrases.

#### Section 3: On-Chain Security — What Happens After You Connect Your Wallet

When you connect your wallet to a DEX, a DApp, or any Web3 site, you interact with smart contracts. Each interaction can request permissions — token approvals that let the contract spend your tokens. These approvals persist until you revoke them.

Think of a token approval like giving someone a signed blank cheque. They can cash it whenever they want, for as much as you authorised. An "unlimited approval" is a blank cheque with no limit. Most DEXs request unlimited approvals by default because it's convenient — you don't need to re-approve for every trade. But convenience is risk.

"Verified contract" on a block explorer means the source code is published — not that the code is safe. Many scam contracts are verified. Honeypots, rug pulls, and drainers often have verified source code because they want to look legitimate. Verification means transparency, not safety.

**Practical takeaway:** Revoke approvals you're not actively using. Check your active approvals on your chain's block explorer (bscscan.com/tokenapprovalchecker for BSC). Run `/clawback-token-check [your-wallet]` to scan for risky approvals. Set up `/clawback-watchdog` for continuous monitoring.

#### Section 4: Social Engineering — The Human Layer

Every scam in this training guide exploits the same thing: human psychology. The phishing site is just HTML. The honeypot is just code. The pig butchering platform is just a dashboard. What makes them work is the emotional manipulation that gets you to interact with them.

Robert Cialdini identified seven principles of influence that scammers weaponise: Authority (impersonating officials), Scarcity (fake urgency), Social Proof (manufactured consensus), Reciprocity (fake help that creates obligation), Commitment (small agreements escalating), Liking (rapport and attraction), and Unity (shared group identity). ClawBack Analyse names these principles explicitly when it analyses suspicious messages — once you can name the weapon, it loses power.

The single most important rule in crypto security is this: **if they contacted you first, be suspicious.** Legitimate exchanges don't DM you on WhatsApp. Real crypto analysts don't approach you on dating apps. Genuine airdrops don't require you to "claim" on a third-party site. The moment someone reaches out to you about your money, your default should be distrust until verified.

**Practical takeaway:** Learn to name the technique, not just feel the pressure. When a message makes you feel urgent, scared, or lucky — that is the manipulation working. Pause. Verify. Run `/clawback-analyse` on the message. The analysis names the psychological weapons so you can see the machinery behind the emotion.

---

## Threat Briefing

Current-landscape overview of crypto scam trends. LLM-generated based on training knowledge, framed as educational context rather than real-time intelligence. Serves as a good demo entry point.

```
============================================================
CLAWBACK THREAT BRIEFING
============================================================

CURRENT LANDSCAPE (as of training data)

------------------------------------------------------------
TOP THREATS
------------------------------------------------------------
1. AI-Generated Scams — Scam messages are now grammatically
   perfect, and AI voice cloning can replicate anyone's voice
   from a few seconds of audio. Deepfake video calls are being
   used in pig butchering operations to pass video verification.
   You can no longer spot a scam by bad grammar OR by "just
   video call them." Focus on WHAT they ask for, not HOW
   convincing they seem.
   → /clawback-learn deepfake

2. Pig Butchering — The fastest-growing crypto scam category.
   Average loss: $50,000-$200,000. Originates on dating apps
   and social media. 3-week minimum rapport before the ask.
   → /clawback-learn pig butchering

3. Approval Exploits — Stale token approvals from months ago
   are being exploited. A contract you approved in January
   could drain your wallet in March if it gets compromised.
   → /clawback-learn token approval

4. Recovery Scams — Targeting people who already lost money.
   "We can recover your funds" is always a second scam.
   → /clawback-learn scams (recovery scam)

5. Honeypot Airdrops — Tokens appearing in wallets designed
   to lure interaction with malicious contracts. If you didn't
   buy it, don't touch it.
   → /clawback-learn honeypot

------------------------------------------------------------
KEY STATS
------------------------------------------------------------
- Crypto scam losses reached $17B in 2025 (Chainalysis)
- AI voice cloning and deepfake video are now standard tools
  in pig butchering and impersonation operations
- Pig butchering is the highest-revenue scam category
- Approval-based exploits are growing as DeFi usage increases
- Most victims are NOT beginners — experienced users get
  targeted with more sophisticated techniques

------------------------------------------------------------
YOUR DEFENCE STACK
------------------------------------------------------------
1. /clawback-learn — Understand how scams work (you are here)
2. /clawback-train setup — Harden your exchange security
3. /clawback-watchdog — Monitor your wallet 24/7
4. /clawback-analyse — Check suspicious messages in real time
5. /clawback-token-check — Verify any token before buying
6. /clawback-url-scan — Verify any link before clicking

============================================================
```

---

## What This Skill Is NOT

- **Not a quiz or interactive trainer** — use `/clawback-train` for practice, quizzes, and security setup walkthroughs
- **Not a threat scanner** — use `clawback-analyse` for real suspicious messages, `clawback-token-check` for real tokens, `clawback-url-scan` for real URLs
- **Not a monitoring service** — use `clawback-watchdog` for ongoing wallet monitoring
- **Not a reporting tool** — use `clawback-report` to generate formal incident reports

This skill is the **reference library** — it builds the knowledge that makes every other ClawBack skill more effective. Understanding how a honeypot works makes the token-check results meaningful. Recognising Authority impersonation makes the analyse reports click. Knowing what a token approval is makes the watchdog alerts actionable.

---

## Integration with ClawBack Suite

ClawBack Learn is the knowledge base that makes every other skill more effective.

| User Situation | Trigger Skill | What to Say |
|---|---|---|
| User reads about token security concepts | `clawback-token-check` | "Ready to check a real token? Run `/clawback-token-check [address]` to see these concepts in action." |
| User reads about phishing/URL concepts | `clawback-url-scan` | "Want to check a real URL? Run `/clawback-url-scan [url]` to see domain analysis in action." |
| User reads about social engineering | `clawback-analyse` | "Got a suspicious message? Run `/clawback-analyse` and paste it for a real Cialdini breakdown." |
| User finishes reading and wants to practise | `clawback-train` | "Ready to test your knowledge? Run `/clawback-train quiz` for 5 rounds of scam detection practice." |
| User reads about wallet monitoring | `clawback-watchdog` | "Want 24/7 protection? Run `/clawback-watchdog [your-wallet-address]` to monitor your wallet automatically." |
| User needs to report a real incident | `clawback-report` | "If this isn't training — if you've been targeted for real — run `/clawback-report` to generate a formal incident report." |

---

## About the Builder

**Vance Lever** ([@LeverCRO](https://x.com/LeverCRO)) — AI CRO, agentic economy builder, and crypto security researcher.

The methodology behind ClawBack is not theoretical. It draws on years of professional experience in social engineering defence — including published research on manipulation techniques, presentations at major security conferences on voice phishing (vishing) attack patterns, and hands-on experience building enterprise-grade social engineering simulation and training platforms.

The Cialdini framework, the scam playbooks, the voice phishing detection patterns, and the "name the weapon to break the spell" philosophy come from studying, simulating, and defending against these attacks in the real world — not from reading about them.

ClawBack is built on the principle that the best defence against social engineering is understanding exactly how it works — naming the weapons, mapping the playbook, and breaking the emotional spell that makes intelligent people hand over their keys.
