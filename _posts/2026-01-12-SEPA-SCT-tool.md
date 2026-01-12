---
title: "SEPA SCT Tool v0.1 — Deterministic pain.001.001.09 CLI and Web Demo"
date: 2026-01-11 22:00:00 +0100
categories: [releases, sepa, iso20022]
tags: [sepa, sct, iso20022, pain.001.001.09, cli, determinism, validation, web-demo]
pin: false
---

SEPA SCT Tool v0.1 is an **open‑source, deterministic generator and validator** for
**SEPA Credit Transfer (SCT)** messages based on **ISO 20022 `pain.001.001.09`**
(Customer Credit Transfer Initiation; 2019 message version).

Project repository:  
**[https://github.com/sbozich/sepa-sct-toolkit](https://github.com/sbozich/sepa-sct-toolkit)**

This post serves as a **long‑form introduction** to the project. It complements the GitHub README
and is intended for readers who want to understand *why* the tool exists, *how* it is designed,
and *what problems it deliberately does and does not solve*.

---

## Why This Tool Exists

SEPA payment files are **infrastructure artifacts**, not user‑interface objects.

In many environments, `pain.001` files are:
- generated indirectly by spreadsheets or ERPs,
- validated only at bank upload time,
- rejected with opaque error messages,
- and difficult to reproduce or audit after the fact.

This project takes a different approach:

- correctness before convenience
- determinism before heuristics
- explicit validation stages instead of silent normalization
- offline, inspectable tooling instead of opaque services

The result is a CLI‑first tool that makes validation failures **understandable and reproducible**
*before* files reach a bank or PSP.

---

## Interfaces and Authority Model

The project exposes two interfaces with a **strict authority boundary**:

### CLI (Authoritative)
The CLI is the **sole authoritative implementation**. It performs:

1. Parsing and well‑formedness checks
2. **XSD validation** against a pinned `pain.001.001.09` schema set
3. Explicit business‑rule validation

Only the CLI should be used for production validation or pre‑flight checks.

### Web Demo (Non‑Authoritative)
The Web Demo is a **static, client‑side inspection and learning surface**:

- no backend
- no telemetry
- no data leaves the browser
- **no XSD validation**

It exists to make the structure of SCT messages easier to explore, but it must never be treated
as authoritative.

If the Web Demo and CLI ever disagree, the CLI is correct.

---

## Scope and Constraints (v0.1)

The scope of v0.1 is intentionally conservative.

### Included
- Scheme: **SEPA Credit Transfer (SCT, classic)**
- Message: **`pain.001.001.09`**
- Currency: **EUR only**
- Structure: **exactly one `PmtInf`**
- Inputs:
  - canonical JSON (strict contract)
  - end‑user CSV (normalized deterministically)
- Outputs:
  - `pain.001.001.09` XML
  - canonical JSON validation report
  - human‑readable report

### Explicitly Excluded
- SCT Inst (instant payments)
- Other `pain.001` versions
- Multiple `PmtInf` blocks
- Multi‑currency support
- Bank‑specific acceptance guarantees
- Backend services or hosted validation

These exclusions are **design decisions**, not missing features.

---

## Determinism as a First‑Class Property

By default, the tool is deterministic:

- identical canonical input ⇒ identical XML output
- identical IDs (Message ID, Payment Info ID, End‑to‑End IDs)
- identical reports

This makes the tool suitable for:

- regression testing
- audits
- CI pipelines
- controlled migrations

A random ID mode exists but must be explicitly enabled and is always reported.

---

## Input Model and Validation Philosophy

The tool uses a **canonical JSON model** as its primary input contract.

Key characteristics:
- strict schemas (`additionalProperties: false`)
- no silent field dropping
- no per‑transaction currency (SCT is EUR‑only)
- early rejection of non‑conforming input

This explains why inputs that “look reasonable” at first glance may still be rejected:
the goal is to fail early and explicitly rather than generate files that will later be rejected
by a bank.

CSV input is normalized deterministically into the same canonical model.

---

## Running the Web Demo Locally

The Web Demo is published as **static files**.

From the repository root:

```bash
python3 -m http.server 5173
```

Then open:

```text
http://127.0.0.1:5173/web/demo/index.html
```

Opening files directly via `file://` URLs is not supported due to browser security restrictions.

---

## Customization and Institutional Integration

The public repository targets a **scheme‑pure, standards‑bounded SEPA SCT profile**.

In real‑world environments, banks and institutions often require:
- additional validation rules
- institution‑specific formatting constraints
- integration into existing ERP, treasury, or payroll systems
- alignment with internal compliance workflows

The architecture allows such adaptations, but they are intentionally **out of scope**
for the public repository.

Organizations interested in customization, integration, or institution-specific variants are encouraged to [contact the author](mailto:modblaises@proton.me "modblaises@proton.me")
 to discuss requirements and constraints.

---

## Project Status and Licensing

v0.1 is an **early, intentionally conservative release**.

The project is published as **open source**, with the explicit intent to allow:
- inspection
- experimentation
- forking
- adaptation

A specific OSI‑approved license will be finalized prior to a stable 1.0 release.

---

## Disclaimer

This tool validates against published standards and a documented ruleset but
**does not guarantee bank or PSP acceptance**.

Final acceptance is determined by the receiving PSP and their local validation rules.

---
