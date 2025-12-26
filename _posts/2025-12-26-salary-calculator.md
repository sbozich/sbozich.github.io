---
title: "Introducing GrossNetto – a Free, Privacy‑First Gross↔Net Salary Calculator"
date: 2025-12-26 13:00:00 +0100
categories: [tools]
tags: [salary, payroll, tax, calculator, privacy, web-tools]
---

Over the last weeks I’ve been building a tool I personally wanted for a quite some time:

> **GrossNetto – a free, privacy‑first gross↔net salary calculator that runs entirely in your browser.**  
> Live: [https://grossnetto.com](https://grossnetto.com)

<p align="center">
  <img 
    src="https://raw.githubusercontent.com/sbozich/salarycalc/main/screenshot.png" 
    alt="GrossNetto Screenshot" 
    style="max-width: 70%; border-radius: 8px;"
  >
</p>

It’s a **static, client‑side** calculator: open it, run numbers, close the tab. No signup. No backend. No data sitting on someone else’s server.

---

## Core principles

### 100% client‑side and privacy‑first

- Static HTML/CSS/JS (GitHub Pages)  
- Fronted by Cloudflare for speed and reliability  
- No cookies, no trackers, no accounts, no database  
- Your inputs stay in your browser tab

### Market‑aware logic (not a “one size fits all” widget)

Payroll rules vary by country and change over time. GrossNetto is built with explicit market logic instead of generic guesses, so it can be extended and maintained without breaking the core.

GrossNetto currently implements deterministic, market-specific payroll logic for the following countries:

- Germany (DE)
- Austria (AT)
- United Kingdom (UK)
- France (FR)
- Netherlands (NL)
- Poland (PL)
- Spain (ES)
- Slovenia (SI)
- Italy (IT)
- Sweden (SE)

Its tax logic is updated for the 2026. year for all supported markets. Successive years will be added, retaining already passed ones.

### Transparent outputs

Where possible, the tool favors:

- explicit inputs  
- explicit assumptions  
- clear totals and breakdowns

The goal is not “mystical correctness” but **repeatable, auditable calculation paths**.

---

## How to use it

1. Open **[https://grossnetto.com](https://grossnetto.com)**.  
2. Select your **market** (and any required parameters like tax class).  
3. Enter  **gross wage**.  
4. Review the breakdown and adjust inputs until it matches your scenario.

That’s it. No registration flow. No onboarding funnel.

---

## Under the hood

GrossNetto intentionally stays simple:

- plain HTML markup  
- CSS tuned for fast UI iteration  
- modular JavaScript for:
  - form/state handling  
  - market rules  
  - calculation engine  
  - i18n (where applicable)

If you prefer self‑hosting, you can clone the repository and serve it as a static site.

Repository (code):  
[https://github.com/sbozich/salarycalc](https://github.com/sbozich/salarycalc)

---

## Where this fits in my toolbox

GrossNetto joins my other small, focused utilities:

- **ListComparator** – compare, merge and deduplicate text lists  
- **TextMetricSEO** – quick text metrics with a simple SEO angle  
- **InvoCreator** – offline, privacy‑first invoice generator  
- **GrossNetto** – gross↔net salary calculations without tracking

Same philosophy across all of them:

> Small tools that solve a narrow problem well,  
> without turning the user into the product.

---

## Feedback

If something is unclear, missing, or numerically suspicious, I want to hear about it:

- Suggestions / bug reports: **modblaises@proton.me**

If the tool saves you time (or prevents one spreadsheet rabbit hole), that’s a win.

