---
title: "Introducing InvoCreator – a Free, Privacy‑First Invoice Generator"
date: 2025-11-27 10:00:00 +0100
categories: [tools]
tags: [invoice, invoicing, freelancing, privacy, web-tools]
---

Over the last days I quietly shipped a third tool into my little toolbox family, next to **ListComparator** and **TextMetricSEO**:

> **InvoCreator – a free, privacy‑first invoice generator that runs entirely in your browser.**  
> Live: [https://www.invocreator.com](https://www.invocreator.com)

<p align="center">
  <img 
    src="https://raw.githubusercontent.com/sbozich/invocreator/main/assets/img/sshot.jpg" 
    alt="InvoCreator Screenshot" 
    style="max-width: 75%; border-radius: 6px;"
  >
</p>



Most online invoice tools want an account, a subscription, and your data sitting on someone else’s servers. I wanted something much simpler:

- open a page  
- fill in the fields  
- print or save as PDF  
- close the tab – and nothing is stored anywhere

So InvoCreator is exactly that: a **static, client‑side invoice editor** with a layout tuned for real‑world invoices in multiple languages and markets.

---

## Why I built it

I kept running into the same problems with typical invoicing tools:

- they want you to **register and log in** for even a single invoice  
- they store client / invoice data on their servers  
- they are opinionated about layout, currencies or markets  
- many of them are overkill for simple, occasional invoices

For many freelancers, one‑person businesses, and small shops, that’s not necessary. Sometimes you just need:

- a clean invoice layout  
- correct fields and VAT handling  
- the ability to save as PDF and be done

So InvoCreator is built with a few principles:

- **no signup, no tracking, no backend**  
- everything runs in your browser  
- fully usable offline once loaded  
- easy to export/import as JSON templates

---

## Key features

### 100% client‑side and privacy‑first

- Static HTML/CSS/JS, hosted on GitHub Pages and fronted by Cloudflare  
- No database, no cookies, no analytics, no trackers  
- All data lives only in your browser tab until you print or export

### Multi‑language & market aware

InvoCreator currently supports:

- English (International / EU)  
- English (US)  
- German (DE)  
- Spanish (ES)  
- Italian (IT)  
- Slovenian (SI)  
- Swedish (SE)  
- Bosnian / Croatian / Serbian (BHS)  

Each language/market has its own:

- field labels  
- helper notes  
- documentation / help text  
- VAT / reverse‑charge wording

### Layout tuned for real invoices

The invoice layout includes:

- seller and buyer blocks (with optional logos)  
- itemized rows (quantity, unit, price, tax)  
- subtotals, tax/VAT and final total  
- optional customer number, order reference, notes and footer

It’s designed to look familiar to accountants, clients, and tax offices – not like a random generic template.

### VAT and reverse charge

For EU‑style invoices there is:

- standard VAT percentage per market  
- **reverse charge** toggle where VAT is paid by the recipient  
- localized reverse‑charge explanations per language

### Payment QR, templates and themes

- Optional **payment QR** block (SEPA QR for EUR or generic QR style otherwise)  
- JSON export/import:
  - **Download JSON template** – save current invoice data as `.json`  
  - **Load JSON template** – restore it later  
- Light/dark theme toggle, with print‑friendly layout in both cases

---

## How to use InvoCreator

Using it is intentionally straightforward:

1. Open **[https://www.invocreator.com](https://www.invocreator.com)** in a modern browser.  
2. Choose your **language / market** from the top menu.  
3. Fill in seller, buyer, item lines, VAT, notes, and optional reverse‑charge/QR.  
4. Click **Print invoice** and use the browser dialog to print or save as PDF.  
5. Optionally, export your current invoice as a JSON template and reuse it later.

That’s it. There is no “account”. When you close the tab, it’s gone (unless you save the PDF or JSON somewhere).

---

## Under the hood

Technically, InvoCreator is very simple on purpose:

- plain HTML markup  
- one CSS file for layout and print styling  
- a small JavaScript core for:
  - form state  
  - calculations  
  - QR code generation  
  - JSON import/export  
- an i18n file with all translations and documentation text

There is no build system or framework – you can clone the repository and open `index.html` directly in a browser, or serve it with any static file server.

Repository:  
[https://github.com/sbozich/invocreator](https://github.com/sbozich/invocreator)

---

## How InvoCreator fits into the toolbox

This tool joins my other small, focused web utilities:

- **ListComparator** – compare, merge and deduplicate text lists (emails, SKUs, etc.)  
- **TextMetricSEO** – analyze text length and simple SEO‑oriented metrics  
- **InvoCreator** – generate clean invoices offline, with no tracking

The idea is the same across all of them:

> Small, practical tools that solve a narrow problem well,  
> without user accounts, tracking, or heavy UI.

---

## Feedback and support

If you try InvoCreator and something feels off, confusing or missing, I’d love to hear it:

- Suggestions and comments: **modblaises@proton.me**  

If you find the tool useful and want to support further development, you can:

- **[BuyMeACoffee](https://www.buymeacoffee.com/sbozich)** (also linked in the footer of the app)

---

Thanks for reading, and if InvoCreator saves you a bit of time or removes one SaaS subscription from your life, it’s already doing its job.
