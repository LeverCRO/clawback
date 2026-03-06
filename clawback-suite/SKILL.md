---
name: clawback-suite
description: Complete crypto scam detection suite for OpenClaw. Installs all 7 ClawBack skills — analyse (social engineering detection), token-check (on-chain scanner), url-scan (phishing detection), watchdog (proactive wallet monitoring), train (interactive quizzes), learn (security education), and report (incident documentation). One install, full protection. Activates via /clawback or any /clawback-* subcommand.
metadata: {"clawdbot":{"emoji":"🦞","homepage":"https://clawback.bot"}}
---

# ClawBack

**Crypto scam detection suite for OpenClaw. 7 skills. Zero tolerance.**

Built by Vance Lever ([@LeverCRO](https://x.com/LeverCRO)) — AI CRO, agentic economy builder, crypto security researcher. Part of the [ClawBack](https://github.com/LeverCRO/clawback) skill suite for the Binance OpenClaw AI Innovation Competition.

---

## When This Skill Activates

This skill activates when a user runs `/clawback` with no arguments. It shows the suite menu and routes to the appropriate skill.

Any `/clawback-*` subcommand routes directly to the relevant skill — this meta-skill does not intercept or wrap them.

---

## Suite Menu

When a user runs `/clawback`:

```
============================================================
CLAWBACK — CRYPTO SCAM DETECTION SUITE
============================================================

7 skills. Zero tolerance.

DETECT:
  /clawback-analyse     — Analyse suspicious messages, emails, screenshots
  /clawback-token-check — Scan tokens, contracts, wallets on-chain
  /clawback-url-scan    — Check URLs, domains, phishing sites

PROTECT:
  /clawback-watchdog    — Monitor your wallet 24/7 (silent alerts)

LEARN:
  /clawback-learn       — Security terms, scam playbooks, fundamentals
  /clawback-train       — Interactive quizzes and security setup

RESPOND:
  /clawback-report      — Incident reports, community submissions

NEW HERE?
  Start with /clawback-learn then /clawback-train quiz
  Set up monitoring: /clawback-watchdog 0xYourAddress

clawback.bot · github.com/LeverCRO/clawback
============================================================
```

---

## Quick Start

| Situation | Command |
|---|---|
| New to crypto security | `/clawback-learn` → `/clawback-train quiz` → `/clawback-train setup` |
| Got a suspicious message NOW | `/clawback-analyse` and paste it |
| About to buy a token | `/clawback-token-check [address]` |
| Someone sent you a link | `/clawback-url-scan [url]` |
| Want ongoing protection | `/clawback-watchdog [your-wallet-address]` |
| Already been scammed | `/clawback-report incident` |

---

## Skills

| Skill | Activates On | What It Does |
|---|---|---|
| `clawback-analyse` | `/clawback-analyse` + pasted message/screenshot | Social engineering detection — maps Cialdini manipulation techniques, identifies crypto-specific attack vectors, assigns confidence verdict |
| `clawback-token-check` | `/clawback-token-check [address]` | On-chain token, contract, and wallet scanner via GoPlus API — honeypot detection, approval audits, deployer reputation |
| `clawback-url-scan` | `/clawback-url-scan [url]` | URL and domain phishing detection via VirusTotal + URLScan.io — domain age, engine verdicts, visual clone detection |
| `clawback-watchdog` | `/clawback-watchdog [address]` | Proactive cron-based wallet monitoring — token approval audit, airdrop honeypot detection, address reputation tracking |
| `clawback-learn` | `/clawback-learn` | Security education — 30 key terms, 12 scam type playbooks with Cialdini analysis, 4-part security fundamentals, threat briefing |
| `clawback-train` | `/clawback-train` | Interactive training — 14-scenario quiz, guided security setup, personal training from your wallet data |
| `clawback-report` | `/clawback-report` | Incident reporting, Chainabuse community submission, personal activity digest, weekly community threat intelligence |

---

## Install

Full suite:
```
clawhub install clawback-suite
```

Individual skills:
```
clawhub install clawback-analyse
clawhub install clawback-token-check
clawhub install clawback-url-scan
clawhub install clawback-watchdog
clawhub install clawback-train
clawhub install clawback-learn
clawhub install clawback-report
```

Or give your agent:
```
Read clawback.bot/SKILL.md
```

---

## About the Builder

**Vance Lever** ([@LeverCRO](https://x.com/LeverCRO)) — AI CRO and agentic economy builder with deep expertise in social engineering, manipulation detection, and crypto security. ClawBack is built on the principle that the best defence against social engineering is understanding exactly how it works — naming the weapons, mapping the playbook, and breaking the emotional spell that makes intelligent people hand over their keys.
