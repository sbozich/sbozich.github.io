---
title: "TextMetricSEO — a browser-based word & phrase frequency analyzer"
date: 2025-11-10 10:00:00 +0100
categories: [Tools]
tags: [text, seo, frequency, analyzer, js]
---

I’ve published a new little web tool: **TextMetricSEO** — a privacy-friendly word and phrase frequency analyzer that works entirely in the browser.

The idea is simple: sometimes you just want to paste a big text, see which words dominate, check whether you’re repeating a phrase too often, or quickly estimate keyword density for SEO — **without** signing up, sending the text to a server, or installing anything. This tool does exactly that.

🌐 **Live site:** <https://www.textmetricseo.com>

<p align="center">
  <img src="https://raw.githubusercontent.com/sbozich/word-frequency-analyzer/main/assets/preview.png"
       alt="TextMetricSEO"
       width="800"
       style="border-radius: 10px; box-shadow: 0 2px 8px rgba(0,0,0,0.15); margin: 1.5rem 0;">
</p>

---

## Why?

I needed something similar when I was writing and editing, and I already had a “list comparator” tool (built before, put online recently). So I built another small, self-contained tool, again with these rules:

- static site only (no database),
- no login,
- runs in browser,
- multilingual.

The result is a page that you can host on GitHub Pages, put behind Cloudflare, and forget about.

---

## What it does

### 1. Word frequency
Paste or upload text and the tool will:

- count total words,
- count unique words,
- show the most frequent words in a table,
- calculate percentages for each word.

This is useful when you want to see if your text is too “heavy” on certain filler words, or if the core topic words actually appear often enough.

---

### 2. Phrase counting (bigrams / trigrams)
This is the part most word counters don’t have.

You can switch **“Phrase counting”** to:

- **Bigrams (2 words)** or
- **Trigrams (3 words)**

and the tool will list repeated word combinations in a separate table. That’s great for:

- spotting clichés in your writing,
- finding repeated patterns you didn’t intend,
- checking whether your SEO keyphrase actually appears in the text.

I added it because I wanted something more than “word → count”.

---

### 3. Multilingual support
TextMetricSEO supports:

- **English**
- **German**
- **Serbian / Croatian / Bosnian**

When you pick a language, the tool loads that language’s stopword list and can filter them out (“the”, “and”, “aber”, “und”, “i”, “da”…). That makes the frequency table much cleaner.

Stopwords are just JSON files in `/assets/stopwords/`, so it’s easy to extend.

---

### 4. Text cleaning options
There’s a small panel of options:

- **Case handling** (lowercase all / keep case)
- **Strip punctuation**
- **Ignore numbers**
- **Minimum word length**
- **Filter stopwords**
- **Exclude custom words** (comma-separated)

This matters because sometimes you want raw counts, and sometimes you want a clean list that’s closer to keywords.

---

### 5. Privacy-friendly
Everything runs in the browser. No uploads. No backend. So you can paste sensitive text (drafts, reports, client work) and it never leaves your machine.

---

## Technical notes

- built with plain HTML/CSS/JS
- has `manifest.json` and a service worker → can be used as a mini PWA
- perfect for GitHub Pages + Cloudflare
- mobile-friendly layout
- custom domain: **textmetricseo.com**

---

## Try it

👉 <https://www.textmetricseo.com>


*Created by S. Bozich — privacy-friendly, open-source, and built with ❤️ for the web.*




