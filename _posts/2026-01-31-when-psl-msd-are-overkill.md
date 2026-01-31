---
title: "When PSL & MSD Is Overkill"
categories: [engineering, ai, solo-building]
tags: [ai, governance, trade-offs, solo-projects, systems-thinking]
---

## A clarification, not a retreat

PSL & MSD were **[introduced](https://sbozich.github.io/posts/governing-long-projects-ai/)** as a way to govern long-running, AI-assisted projects without losing intent, correctness, or architectural integrity.

That does *not* mean they should be used everywhere.

In fact, applying PSL & MSD in the wrong context is worse than not using them at all. This article exists to define those boundaries clearly — not to weaken the method, but to prevent misuse.

---

## What PSL & MSD optimize for

Before discussing overkill, it is important to restate the objective function.

PSL & MSD optimize for:
- long-term semantic stability
- explicit recoverability from mistakes
- resistance to AI-driven reinterpretation
- auditability of decisions over time

They do **not** optimize for speed, novelty, or exploration.

If those are your primary goals, you are solving a different problem.

---

## Early ideation and exploration

PSL & MSD are a poor fit for:
- brainstorming
- architectural exploration
- “what if” prototyping
- discovery-driven work

In these phases, ambiguity is not a bug — it is the point.

Locking invariants too early increases the cost of learning and encourages premature certainty. PSL & MSD deliberately resist fluid reinterpretation, which is exactly what early exploration often requires.

Use sketches, notes, throwaway code, or even unstructured AI conversations instead.

---

## Short-lived or disposable projects

If a project is expected to:
- live for days, not months
- be rewritten rather than evolved
- be abandoned without regret

…then PSL & MSD introduce unnecessary friction.

The ceremony only pays off when:
- the cost of restarting is high, or
- the cost of being wrong compounds over time.

Disposable work should remain disposable.

---

## Low-stakes domains

PSL & MSD assume that mistakes matter.

If incorrect behavior:
- is cheap to detect,
- cheap to fix,
- and has no downstream consequences,

then governance-first approaches are misaligned.

Examples include:
- UI-only experiments
- internal tooling with no external contract
- personal scripts with no reuse expectations

In these cases, speed and convenience dominate correctness.

---

## Multi-author teams without governance agreement

PSL & MSD implicitly assume:
- a single authority, or
- a clearly defined governance model.

Without that, immutability becomes contested rather than stabilizing.

In team environments, additional mechanisms are required:
- ownership rules
- arbitration procedures
- merge and versioning policy

Without them, PSL & MSD can become a source of friction rather than clarity.

This is not a flaw — it is an undeclared dependency.

---

## When restarting is cheaper than correcting

Some projects are better reset than repaired.

If:
- errors are easy to detect,
- history is not valuable,
- and clean restarts are culturally acceptable,

then append-only ledgers and immutable specifications are unnecessary.

PSL & MSD exist specifically to make correction safer than restart.  
If restart is already cheap, their advantage disappears.

---

## The common misunderstanding

A frequent misreading is to treat PSL & MSD as “heavy documentation”.

They are not.

They are governance mechanisms designed to constrain evolution intentionally. When those constraints are unnecessary, they feel obstructive — correctly so.

Overkill is not a moral failing.  
It is a signal of misaligned optimization.

---

## The intended boundary

Use PSL & MSD when:
- the project will outlive individual work sessions
- AI is used repeatedly, not once
- correctness is cumulative
- you expect to discover that earlier decisions were wrong

Avoid them when:
- exploration matters more than preservation
- restarting is cheaper than fixing
- ambiguity is still productive

---

## Closing note

Every serious method is overkill somewhere.

PSL & MSD are no exception — and that is a feature, not a weakness.

They exist for projects where stability is more valuable than speed, and memory is more valuable than momentum.

Outside that boundary, simpler tools win — and should.
