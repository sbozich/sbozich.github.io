---
title: "PSL & MSD: Governing Long‑Running Projects in the Age of AI"
categories: [engineering, ai, solo-building]
tags: [ai, governance, documentation, solo-projects, systems-thinking]
---

## Why this exists

Anyone who has seriously used AI as a project partner has encountered the same failure mode:  
**context drift**.

Across long‑running projects, intent slowly erodes. Decisions are reinterpreted, constraints loosen implicitly, and earlier conclusions are quietly overridden. This happens with humans; with AI, it is amplified. Statelessness, probabilistic reasoning, and helpfulness bias all work against long‑term determinism.

After repeatedly hitting these limits, I converged on a strategy that solved the problem not by better prompting, but by **governance**.

That strategy is what I call **PSL & MSD**.

---

## The core idea (in one sentence)

Split the project into:
- one document that defines **what is allowed to exist**, and
- another that records **everything that has happened**, without rewriting history.

Everything else follows from that separation.

---

## MSD — the immutable specification layer

**MSD (Master Specification Document)** is the project’s constitutional layer.

It defines:
- scope and explicit non‑scope
- invariants and guarantees
- architectural constraints
- semantic definitions
- forbidden actions

Once established, the MSD is **not silently editable**.  
If something fundamental changes, the MSD is versioned explicitly. Otherwise, it remains fixed.

This immutability matters because it anchors both the human and the AI. When ambiguity arises, the question is never “what feels right now?”, but “what does the MSD permit?”.

Crucially, MSD is not documentation *about* the system.  
It is the system’s normative definition.

### Micro‑example (MSD)

> *Invariant:* “Employer‑side taxes must never reduce employee net salary.”  
>  
> Any implementation or suggestion that violates this is invalid by definition, even if it appears numerically convenient or UX‑friendly.

This single sentence eliminates entire classes of accidental regressions — for both humans and AI.

---

## PSL — the cumulative project ledger

**PSL (Project State Ledger)** is append‑only.

It records:
- phases and hard gates
- decisions and their rationale
- discovered errors
- invalidated assumptions
- resets and reopenings

Nothing is deleted. Mistakes are not erased; they are **marked and superseded**.

This turns PSL into something closer to an event log than a roadmap. It preserves causality. It allows both humans and AI to replay how the project arrived at its current state — including where it went wrong.

That property is what makes correction safe instead of destructive.

### Micro‑example (PSL)

> *Phase B conclusion (later invalidated):*  
> “SEPA SCT allows field X to be optional.”  
>  
> *Status:* Invalidated in Phase C after regulatory review.  
> *Action:* Phase B reopened; dependent conclusions frozen.

The original reasoning remains visible, but no longer authoritative.

---

## Why this matters specifically for AI

AI systems do not “remember” projects — they infer them.

Without hard boundaries, inference fills gaps with plausibility. Over time, that leads to:
- silent scope creep
- reinterpretation of constraints
- regression of previously settled decisions

MSD acts as a hard boundary.  
PSL acts as a replay buffer.

Together, they drastically reduce ambiguity without requiring brittle prompt engineering or repeated restatement of intent.

In practice, this means you can resume a complex project weeks later — with a fresh AI session — and regain correctness instead of improvisation.

---

## What this enabled in practice

I validated this approach across two completed projects:
- a **[multi‑market salary calculator](https://sbozich.github.io/posts/salary-calculator/)** with country‑specific legal logic
- a **[SEPA SCT tooling project](https://github.com/sbozich/sepa-sct-toolkit)** that required a full baseline reset mid‑development

In the SEPA case, an early architectural assumption was later proven wrong. Instead of deleting work or pretending it never happened, the project:
- reopened the relevant phase
- invalidated the affected evidence
- preserved the original reasoning for auditability

The project survived because governance was explicit.

Most projects do not fail because of mistakes.  
They fail because they cannot **recover** from them.

---

## What this is not

This is not:
- Agile
- Scrum
- Waterfall
- prompt engineering

It is closer to governance‑first engineering:  
establishing what may exist before deciding what to build next.

It is also intentionally **not lightweight**.

---

## Trade‑offs and limits

This strategy has costs.

It is:
- ceremony‑heavy
- discipline‑dependent
- optimized for solo builders or solo + AI workflows

It is **[not suitable](https://sbozich.github.io/posts/when-psl-msd-are-overkill/)** for:
- exploratory hacking
- throwaway prototypes
- early ideation phases

It also assumes a single authority. Multi‑author use would require explicit arbitration and merge governance, which this version does not define.

These are conscious constraints, not oversights.

---

## When to use it

PSL & MSD make sense when:
- correctness matters more than speed
- the project spans weeks or months
- AI is used repeatedly, not once
- you expect to be wrong at least once and want to survive it

If none of those apply, simpler approaches are better.

---

## Closing thought

AI did not force this strategy into existence — it merely exposed the weaknesses of informal project memory.

PSL & MSD are a response to that exposure.

They do not make projects easier.  
They make them **stable**.

For certain kinds of work, that trade is worth it.
