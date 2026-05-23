---
name: god-loop
description: "Experimental meta-skill. Derive a repo-specific prompt for a loop that homeostates across MODES (goal · frontier · greenfield · story) — each iteration picks the most disturbed mode and runs its playbook. v0; not yet dogfooded."
---

# God Loop  *(v0 — experimental)*

A **meta-loop**. Where each of the four sibling skills derives a prompt for
*one* loop mode, `god-loop` derives a prompt that picks **which mode** to
run this iteration. It is homeostasis *lifted one level* — where
`frontier-loop` homeostates over five axes within a mode, `god-loop`
homeostates over **modes themselves**.

The four family modes:

| Mode | What this mode does | Disturbance signal (shape) |
|---|---|---|
| **goal** | Close a finite acceptance inventory; halt on `criteria-met`. | Open inventory has unsatisfied criteria. |
| **frontier** | Move an existing evidence-backed capability frontier. | Frontier vector dimensions are stagnating; evaluator could be sharper. |
| **greenfield** | Discover an undefined target. | Artifact / metric / target undefined; rubric unstable. |
| **story** | Discover / verify user-facing product promises. | Storyboard sparse or stale; promises uncorroborated. |

Each iteration: sense each mode's disturbance, pick the most disturbed,
run *that mode's* per-iteration playbook. Switching modes between
iterations is expected, not exceptional.

> **Status: v0, experimental.** Not yet dogfooded. The four-skill family is
> stable; `god-loop` is the meta-composer being tested. Most projects do
> better with a single-mode skill.

The per-iteration playbook lives in
[`references/god-prompt-template.md`](references/god-prompt-template.md).

## When NOT to use god-loop

Almost always. If the next 50 iterations are clearly one mode, use that
mode's skill directly. god-loop's mode-classification overhead is *cost*
per iteration; pay it only when the mode actually changes within a run.

Use god-loop when:

- A long-running project genuinely shifts between modes within one run
  (discovery → frontier improvement → goal closure → story verification).
- You don't know upfront which mode applies, and want the loop to figure
  it out — and re-figure it as the work evolves.

Don't use god-loop when:

- Single-mode work covers the whole run.
- The repo is small enough that one skill's discipline is sufficient.
- The mode is already obvious; god-loop's classifier overhead is pure
  ceremony.

## Core framing

A god-loop iteration is **classify → delegate → reclassify**:

1. **Sense** each active mode's disturbance signs in the current repo state.
2. **Diagnose** the most disturbed mode.
3. **Delegate** — execute that mode's iteration protocol (inlined in the
   emitted prompt at derivation time).
4. **Reclassify** at the start of the next iteration.

The mode label is *the outcome*, not the input. Just as `frontier-loop`
picks an axis intervention from disturbance, `god-loop` picks a mode
from disturbance.

## Frontloading discipline

Loops fail when they self-terminate blocked on a decision the user could
have made before the loop fired. god-loop's frontload audit has an extra
job: **deciding which modes are even relevant** for this run, so the
emitted prompt only inlines the playbooks that matter.

At derivation time, for every thing the loop might need (modes active,
per-mode disturbance signs, switch policy, …), do one of:

1. **Resolve it now.** AskUserQuestion if available; otherwise print
   prominently.
2. **Default + Alignment Review.** Pick the smallest reversible default
   and record an Alignment Review.
3. **Mark as escalate candidate.** Only for the truly irreversible.

Anything left over is a **derivation gap** — flagged at iteration time
via the halt-cause classifier.

## Derivation procedure

### Pre-flight: Frontload audit

Walk this checklist. For each: **resolve** / **default + Alignment Review**
/ **escalate-mark**. Record in `loop/STATE.md` under `frontload:` and
prepend a frontload preamble to the emitted prompt.

- **Motive** — one-sentence overall goal.
- **Active modes** — which of {goal, frontier, greenfield, story} actually
  apply during this run? Two-to-three is typical; one means use that
  mode directly; four is rare and possibly a scoping signal.
- **Per-mode disturbance signs** — for each active mode, repo-specific
  signals.
- **Per-mode artifacts** — what files / state does each active mode read
  and write? (e.g., goal → `loop/ACCEPTANCE.md`; greenfield →
  `loop/RUBRIC.md` + `loop/INTENT.md`; story → `docs/storyboard.*`.)
- **Switch policy** — switch cost, max-thrash threshold, quiet-signal halt.
- **Family present?** — does the host have `goal-loop`, `frontier-loop`,
  `greenfield-loop`, `story-loop` installed? god-loop reads their
  templates at derivation time; the emitted prompt is self-contained
  after that.

If only one mode classifies as active during the audit, **decline to
emit a god-prompt** — print a short note routing to that mode's sibling
skill directly.

### 1. Audit which modes are relevant

For each of {goal, frontier, greenfield, story}, ask: does this repo
have evidence the mode *might* apply during this run? Drop inactive
modes from the classifier.

### 2. Author per-repo disturbance signs

For each active mode, name what disturbance looks like in this specific
repo. Examples:

- **goal:** "an open `loop/ACCEPTANCE.md` has criteria not at `PASS`."
- **story:** "the storyboard at `docs/storyboard.md` has `candidate` rows
  older than 14 days."
- **frontier:** "the frontier-vector cost-latency dimension has no
  before→after delta in the last 5 iterations."
- **greenfield:** "INTENT.md has fewer than 3 live hypotheses, or any
  hypothesis has invalidating evidence not yet processed."

These are the signals the loop uses to classify mode each iteration.

### 3. Inline active modes' playbooks

For each active mode, copy that mode's per-iteration playbook (from the
sibling skill's `*-prompt-template.md`) into the emitted prompt under a
`## Mode: <name>` section. This makes the emitted prompt self-contained
— the loop doesn't re-load sibling skill content at iteration time.

If the host doesn't have a sibling skill installed, derivation fails
loudly here. god-loop assumes the loopy family is present.

### 4. Set switch policy

- `{{SWITCH_COST}}` — string explaining when to stay vs switch.
  Default: stay if the current mode showed measurable progress in the
  last iteration; switch freely when it didn't.
- `{{MODE_QUIET_N}}` — default `3`. Iterations with no active disturbance
  in any mode before `god-halt`.
- `{{MAX_MODE_THRASH}}` — default `4` switches per `8` iterations without
  measurable progress in any mode → emit `mode-thrash`.

### 5. Assemble + emit

Start from
[`references/god-prompt-template.md`](references/god-prompt-template.md).
Fill placeholders, inline active-mode playbooks.

### 6. Emit

- `loop/PROMPT.md` — the composite god-prompt.
- `loop/STATE.md` — mode history, current mode, switch counters,
  disturbance log per iteration, `Next action: HALT` hatch.
- Mode-specific artifacts created by each active mode's playbook on its
  first iteration (the playbooks own those).
- A short rationale: which modes were active, switch policy, expected
  failure modes for this specific repo.

## Failure modes

- **Mode thrashing** — switching every iteration without measurable
  progress in any mode. Mitigation: switch-cost rule + `mode-thrash`
  halt.
- **Mode monopoly** — never switching despite signals in other modes.
  Mitigation: disturbance-strength comparison forces a switch when
  another mode shows stronger disturbance.
- **Wrong shape** — using god-loop when single-mode is sufficient.
  Mitigation: derivation's "active modes" gate refuses to emit if only
  one mode classifies; iteration's classifier can also emit
  `wrong-shape` and reroute.
- **Inlined drift** — sibling skill templates update; god-loop's
  inlined playbooks go stale. Mitigation: fingerprint each inlined
  playbook's source version at derivation; surface drift as a
  `derivation-gap` at next re-derivation.

## Diagnostic mode

Invoked to diagnose a drifting god-loop:

1. Read `loop/PROMPT.md`, `loop/STATE.md` (mode history + switch log),
   recent iteration logs.
2. Score: which modes have shown disturbance? Which have shown
   *progress*? Is the loop thrashing, monopolizing, or wrong-shaped?
3. Emit a minimal PROMPT.md mutation — adjust disturbance signs,
   switch policy, or the active-modes list — not a full rewrite.
4. Write a ⚠️ block to `loop/STATE.md` for the next iteration.

## References

- [`references/god-prompt-template.md`](references/god-prompt-template.md)
  — the output template; placeholders for the active modes get filled
  with verbatim content from sibling skill templates at derivation time.
