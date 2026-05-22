---
name: spark-loop
description: "Derive a repo-specific prompt for a fast, closure-shaped improvement loop that seals one operator-facing seam per iteration with explicit proof. Also diagnoses a drifting fast-model loop."
---

# Spark Loop

This skill produces a repo-specific prompt for an external harness running a
fast closure-shaped model. The harness runs the loop; this skill derives the
prompt and the closure discipline that keeps the loop narrow, evidence-backed,
and honest.

The emitted prompt lives in
[`references/spark-prompt-template.md`](references/spark-prompt-template.md).
This file explains how to decide whether a Spark loop is appropriate and how to
fill the template.

## Core framing

A Spark loop is a **seam-closure** loop:

- one live seam per iteration
- one live anchor per accepted change
- one narrow proof surface per seam
- one small reversible diff

It does not search broadly. It cashes out already-known product, performance,
or failure-legibility seams quickly and cleanly.

The short version:

- the frontier loop moves the frontier
- the Spark loop seals its seams

## When to use it

Use this skill when the repository is already past evaluator bootstrap and the
next useful work is closure-shaped:

- resolving review feedback
- closing a reproducible operator-facing bug
- tightening a failure envelope
- removing measured avoidable work from a real path
- hardening a known false-green zone with a discriminative selector
- compressing a repeated workaround into a canonical path

## When not to use it

Do **not** force Spark onto a repo that still needs frontier work.

Prefer `frontier-loop` when:

- the repo lacks a cheap inner channel
- there is no narrow proof channel for the seams you care about
- the main problem is evaluator blindness or ramp incompleteness
- the work is architectural search, not closure
- the model needs wide context more than fast local iteration

If the repo is almost closure-ready but missing one critical proof surface,
allow at most one bootstrap move to create that surface, then return to seam
closure immediately.

## Closure-fit check

Before emitting a Spark prompt, inspect:

1. build / test / validate commands
2. strongest memory surfaces (review findings, run artifacts, ledgers, commits)
3. live operator seams (open findings, failing traces, perf regressions,
   repeated workarounds)
4. proof surfaces (selectors, focused integration tests, perf traces)

Classify the repo:

- **closure-ready**: cheap inner channel exists, at least one narrow proof
  channel exists, and there is a live seam inventory
- **partial-closure**: cheap inner channel exists, but one proof surface is
  missing; one bootstrap move is allowed
- **not-fit**: no cheap inner channel, no narrow proof channel, or no credible
  seam inventory; use `frontier-loop` instead

## Derivation procedure

### 1. Inspect the evidence surface

Read the repo's actual commands and artifacts. Record:

- cheap inner channel
- narrow proof channel
- outer channel
- strongest available memory surfaces, in this order:
  1. externally reviewed findings
  2. typed or machine-derived artifacts
  3. self-authored ledger prose
  4. commit narratives

If only weak surfaces exist, say so explicitly in the emitted prompt.

### 2. Identify live seam classes

Find the seams that matter now. Typical Spark seam families:

- operator-facing ambiguity
- degraded failure legibility
- empty/no-op paths doing unnecessary work
- selector gaps around a known false green
- repeated multi-step workaround
- wrapper behavior that hides or distorts real outcomes

Turn the repo's actual seams into a short priority list for the prompt.

### 3. Define the reward channels

Every Spark prompt needs three channels:

- **Cheap inner channel**: runs every iteration
- **Narrow proof channel**: smallest selector or reproducer that settles the seam
- **Outer channel**: broader proof run used at checkpoints or after cross-flow claims

If one is missing, either allow one bootstrap move or decline Spark.

### 4. Encode seam discipline

The emitted prompt must enforce:

- exactly one live anchor per accepted change
- exactly one seam family per accepted change
- proof before acceptance
- artifact discipline: keep only truth-bearing artifacts
- same-family admissibility: repeated same-shape work needs a fresh trace or new anchor
- fake-progress detectors: no freshness churn, wrapper churn, or unmeasured perf theater

### 5. Scope the loop

Add repo-specific scope if the user gave it or the repo strongly implies it:

- preferred edit surfaces
- forbidden areas
- proof commands that must run for certain subsystems
- forbidden shortcuts

### 6. Assemble the prompt

Start from
[`references/spark-prompt-template.md`](references/spark-prompt-template.md).
Fill in:

- motive
- closure posture (`closure-ready` or `partial-closure`)
- allowed bootstrap note if needed
- cheap / narrow / outer channels
- trusted artifact surfaces
- live anchor classes
- priority seams
- scope and forbidden shortcuts
- quiet-signal checkpoint count

### 7. Emit

Return:

- the final repo-specific Spark prompt
- a short rationale:
  - why Spark fits
  - which channels it will use
  - whether one bootstrap move is sanctioned
  - what the strongest live seams are

If the repo is `not-fit`, do not emit a Spark prompt as if it were safe.
Explain why and route to `frontier-loop`.

## Quality bar

A good Spark prompt makes the model:

- pick one seam, not three
- prove closure, not just claim it
- preserve only artifacts that settle the seam
- stop when the signal goes quiet instead of mining polish

A bad Spark prompt produces:

- freshness churn
- wrapper churn
- repeated same-family micro-edits with no fresh trace
- performance claims with no before/after evidence
- bookkeeping presented as product progress

