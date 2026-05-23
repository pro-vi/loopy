---
name: story-loop
description: "Derive a repo-specific Story Loop prompt that maintains a living product storyboard for frontend/product behavior — extract, reconcile, implement or verify one focused story, and record evidence from prompts, docs, code, and observed UI."
---

# Story Loop

This skill derives a repo-specific Story Loop prompt. The emitted prompt
lives in [`references/story-prompt-template.md`](references/story-prompt-template.md);
the runner runs the prompt, this skill writes it.

Invoke **once per run** to author or revise the prompt. The skill's durable
artifact is the **storyboard**: a reviewable inventory of product promises,
where each promise records where it came from, how confident we are, what
would prove it, and what the latest evidence says.

## Core framing

A Story Loop is a **story-frontier loop**: each iteration discovers one
user-facing promise, makes narrow reversible product judgments when needed,
documents the alignment cost for human review, implements or verifies one
focused slice, captures evidence, and continues. It is not broad QA or a
project-management digest.

The storyboard is an *index of evidence and intent*, not the source of
intent itself. Authority lives in human prompts, current docs, accepted
issues/PRs, and reviewer guidance — re-check those before treating an old
row as truth (see the template's Forbidden Moves).

## Relationship to other skills

These are *optional integrations*; if they are not available in the host
repo, Story Loop performs the responsibility directly or emits a handoff.

- `work` — optional guidance adapter for synthesizing vault, Linear,
  GitHub, Slack, and code context. Do not make the skill depend on
  project-specific work surfaces.
- `acceptance` — verifies known acceptance criteria and emits evidence
  reports after a story has criteria.
- `e2e-test` — owns browser / dev-server mechanics when verifying a
  running app.
- `spark-loop` or `build` — may fix larger product failures after Story
  Loop classifies them. Story Loop itself may still implement one narrow,
  reversible, evidence-backed story per iteration.
- `frontier-loop` — appropriate when the storyboard exposes evaluator
  blindness, missing proof surfaces, or a broader frontier question.

## Derivation procedure

### 1. Inspect the evidence surface

Read the repo to ground the derivation:

- Existing storyboard, if any (`docs/storyboard.{md,yaml}` or similar).
  Note format and whether rows are current.
- Strongest available guidance sources (see template `## Core objects`):
  human prompt + current branch context, vision/product docs, README +
  routes + UI copy, tests + Storybook, issues/PRs/changelogs/reviews,
  observed running UI, analytics/support notes.
- Proof surface inventory: dev-server runnable? `app_url` known? fixtures
  / seed / auth requirements? evidence-capture conventions?
- Selected branch, recent PRs, and any retro notes from prior Story Loop
  runs.

Record what is present and what is missing. Missing proof surfaces become
either `fixture-gap` / `env-gap` candidates (default judgment + log) or
escalates (rare, irreversible-only).

### 2. Select lane and surface class

Identify the lane and the selected surface class:

- Use the user's prompt when explicit; otherwise infer from the strongest
  guidance source and record the inference as an Alignment Review.
- The surface class should be **generic and repo-local** — the
  audience/workflow/surface names the project itself uses. Do not
  hard-code product-specific audiences in the emitted prompt.
- Use **Surface Taste Lane** when the goal is visual / product / frontend
  quality. Use a domain-specific lane name when the user names one
  (e.g., "compliance evidence", "checkout").

A story is in-lane when it improves the selected persona's rendered
surface, workflow, state, decision, trust, or recovery on the selected
surface class.

### 3. Choose the storyboard format and path

Prefer the repo's existing convention. If none exists:

- `docs/storyboard.md` for human review.
- `docs/storyboard.yaml` for machine iteration.

The emitted prompt will operate on this single file; rows follow the
Story Record shape in the template.

### 4. Collect repo-specific inputs

From the user or by inference:

- **Motive** — one-sentence goal (e.g., "verify the evidence-anchor
  workflow on the production-data fixture").
- **Lane** + **surface class** (from step 2).
- **Storyboard path** (from step 3).
- **Fixtures / app URL** when verification is in scope.
- **Forbidden shortcuts**, if the user gave any.

### 5. Assemble the prompt

Start from [`references/story-prompt-template.md`](references/story-prompt-template.md).
Fill in:

- `{{MOTIVE}}`
- **Runner contract** — sibling skills share an identical block; keep in
  sync.
- **Judgment default** — sibling skills share an identical block; keep in
  sync.
- `{{LANE}}`, `{{SURFACE_CLASS}}`
- `{{STORYBOARD_PATH}}`

Drop the **Surface Taste Lane** section when the lane is not the taste
lane.

### 6. Emit

Return:

- `loop/PROMPT.md` — the filled-in template, ready for any runner
  (`/loop`, `/goal`, or an external harness). The prompt is the same; the
  runner only differs in invocation.
- `docs/storyboard.{md,yaml}` — initial storyboard if absent; otherwise
  unchanged.
- `loop/STATE.md` — phase, iteration counter, selected lane, selected
  surface, current story (`none` initially), last action, next action,
  and a `Next action: HALT` exit hatch the user controls.
- A short rationale: chosen lane, chosen surface class, chosen storyboard
  path/format, whether the proof surface is ready or which gap blocks
  verification.

## Diagnostic mode

Invoked to diagnose a drifting Story Loop (not author a new prompt):

1. Read the current `loop/PROMPT.md`, `docs/storyboard.{md,yaml}`, the
   most recent storyboard updates, and `loop/STATE.md`.
2. Score against the template's Forbidden Moves and iteration protocol:
   which rules are encoded? Which is the loop violating?
3. Identify the dominant failure mode: stale storyboard treated as
   authority? Repeated re-verification of easy stories? Broad QA sweep
   masquerading as verification? Verification on unaligned candidates?
   Escalate-polling where judgment should default?
4. Emit a *minimal* PROMPT.md mutation that closes the failure mode —
   not a full rewrite. The hand-evolved prompt has hard-won lessons; the
   retrofit adds what's missing without losing them.
5. Write a ⚠️ block to `loop/STATE.md` telling the next iteration what
   changed and what to do differently.

## References

- [`references/story-prompt-template.md`](references/story-prompt-template.md)
  — the output template. The per-iteration playbook lives here and gets
  populated at derivation time.
