---
name: frontier-loop
description: "Derive a repo-specific prompt for an evidence-driven overnight/persistent improvement loop consumed by an external harness (gnhf, cocc, ralph). Also diagnoses a drifting loop."
---

# Frontier Loop

This skill derives a repo-specific loop prompt for an external harness (gnhf,
cocc, ralph). The harness runs the loop; this skill produces the prompt plus
invariants that make the loop frontier-shaped rather than oracle-shaped.

Invoke **once per run** to author or revise the prompt. The loop's
per-iteration playbook lives in [`references/frontier-prompt-template.md`](references/frontier-prompt-template.md)
as the output template — not in this file.

## Core framing

A Ralph loop is **frontier-seeking**: each iteration moves the repository's
evidence-backed capability frontier. An Oracle loop is **terminal**: it
closes on a finite checklist. Both are valid; this skill derives the
frontier kind.

A healthy frontier loop alternates between improving the product and
improving the mechanism that judges the product. If measurement is weak,
improve measurement first. If measurement is trustworthy, improve the
product.

The loop is **homeostatic**: each iteration senses the repository's state
across a set of axes, diagnoses the most disturbed axis, and applies the
intervention that restores balance. The work-type label (`product` /
`evaluator` / `observability` / `specification` / `review-closure` /
`stop-and-summarize`) describes the intervention; it is not chosen from a
menu.

Valid progress is any small reversible change that does at least one of:

1. improves a meaningful product metric or behavior
2. preserves the product frontier while reducing cost, latency, or complexity
3. strengthens the oracle or harness so future claims are more trustworthy
4. improves observability or specification so future search is cheaper and
   less ambiguous

## Derivation procedure

### 1. Inspect the evidence surface

Read build / test / run commands (from `package.json`, `Cargo.toml`,
`Makefile`, `CLAUDE.md`, `README`), failing selectors, benchmark outputs,
active prompts.

Inspect memory surfaces in priority order — this ranking shapes what
the derived prompt treats as trustworthy signal:

1. **Externally reviewed findings** (strong) — human / external-review
   output that the loop did not author.
2. **Typed / machine-derived artifacts** (strong) — structured run
   traces, harness state, oracle verdicts under `.loop/*`,
   `.gnhf/runs/*`, `benchmarks/`, etc.
3. **Self-authored ledger prose** (weak) — `docs/*-review.md`,
   `BACKLOG.md`, `docs/loopd-e2e-review.md`, or similar
   agent-maintained markdown.
4. **Commit log** (weakest) — git history; always available, but
   self-narrated recency, prone to re-certifying drift.

Record which tier is actually present. The derivation uses this in
step 2.7 and step 6.

### 2. Determine evaluator maturity

Use the T0–T6 ladder in [`references/evaluator-maturity.md`](references/evaluator-maturity.md).
Record the current tier. Note whether the ramp threshold (T3) is met.

### 2.5. Assess ramp need

Walk the ramp stage checklist (0–9) in
[`references/evaluator-maturity.md`](references/evaluator-maturity.md#ramp-stages).
Identify which stages lack artifacts.

- **Ramp mode** fires if stages 3, 4, 5, or 6 are missing. The loop cannot
  sense its frontier yet and must build its instruments first.
- **Main-loop ready** when stages 1–5 are present. Stages 6–9 become
  ordinary `evaluator` / `observability` work inside the main loop.
- **Ramp encoded in motive** — if the user's prompt's first row/phase is
  ramp-shaped ("build a minimal fixture and prove the lifecycle
  end-to-end"), recognize this. Row 1 is ramp; rows 2+ are main-loop
  dependent on row 1 reaching `ramp-complete`.

Record which stages are closed, which need closing, and whether ramp is
explicit in the motive or must be inserted by the skill.

### 2.7. Assess memory-surface availability

Using the tier inspection from step 1, record which memory surfaces
the repo / harness actually provides:

- **Strong surface present** (tier 1 or 2 exists): anti-collapse can
  key on it; the derived prompt's same-family admissibility rule and
  quiet-signal checkpoint have real substrate to check against.
- **Only weak surfaces** (tier 3–4): anti-collapse coverage is
  degraded. The derived prompt should name this explicitly and
  sanction creating a minimal structured findings surface as a valid
  evaluator-axis intervention.
- **No surfaces at all**: the loop has only the commit log to fall
  back on. Derivation should flag this prominently — the signal
  hierarchy section of the emitted prompt should say so, and the
  first main-loop iteration should treat surface creation as an
  evaluator-axis move.

This step's output feeds step 6: the emitted prompt must not pretend
anti-collapse coverage exists when the substrate does not.

### 3. Identify the two reward channels

Frontier loops need both:

- **Cheap inner channel** — lint, typecheck, compile, smoke, interface
  validation. Runs every iteration.
- **Expensive outer channel** — benchmark, search set, latency / cost / SLO
  metrics. Runs on accepted changes or at checkpoints.

If only one exists, the derived prompt sanctions ramp work to build the
other. If neither exists, derivation routes through bootstrap first.

### 4. Classify branch mode

- **Build mode** — new capability landing. Homeostasis axes weighted
  toward product + evaluator.
- **Closure mode** — architecture landed; work comes from reviewed
  findings or exact probes. Apply [`references/review-closure-overlay.md`](references/review-closure-overlay.md).
- **Mixed** — both; rotate modes in the derived prompt's guardrails.

### 5. Collect repo-specific inputs

From the user or by inference:

- **Motive** — one-sentence goal.
- **Scope manifest** (optional) — allowed / forbidden path globs. If
  provided, the derived prompt gains a scope-drift halt rule.
- **Known false-green zones** — tests that pass without actually
  validating.
- **Forbidden shortcuts** — e.g. `--no-verify`, mocked integration,
  assertion-free fixtures.
- **Artifact locations** — where the ledger, benchmark results, run state
  live (or should live).

### 6. Assemble the prompt

Start from [`references/frontier-prompt-template.md`](references/frontier-prompt-template.md).
Inline:

- **Motive**
- **Core law** (the 4 valid-progress criteria)
- **Signal hierarchy** — the four-tier ranking of memory surfaces from
  step 1. If step 2.7 recorded only weak surfaces available, say so
  explicitly in the emitted prompt; sanction a minimal structured
  findings surface as a valid early evaluator-axis intervention. Do
  not emit language that pretends anti-collapse coverage exists when
  the substrate for it does not.
- **Homeostasis frame** — five axes (oracle trustworthiness, product
  capability, failure legibility, specification coherence, intervention
  diversity), their disturbance signs, and the diagnose-then-intervene
  iteration protocol. Intervention-diversity disturbance signs key on
  the strongest surface available (from step 2.7); fall back to commit
  log as a weaker signal only when no stronger surface exists. Work-type
  labels listed as a reference glossary, not as a menu.
- **Evaluator maturity** — current tier, ramp guidance if below T3
- **Reward channels** — cheap + expensive classification from step 3
- **Scope manifest + drift halt** if provided
- **Closure discipline (FIXED ≠ CLOSED):**
  > A change authored by the iteration is `FIXED_PENDING_CONFIRMATION`,
  > not `CLOSED`. Closure requires either the next iteration's review
  > pass explicitly confirming, or the next pass not re-raising the
  > finding. Halt conditions count OPEN only;
  > `FIXED_PENDING_CONFIRMATION` is not cleared.
- **Status-theater prohibition:**
  > Do not emit upfront plans or rollout narration. Do not produce
  > completion summaries mid-run. Traces, diffs, and oracle outputs are
  > truth; notes are memory.
- **Same-family admissibility (forcing function):** when diversity
  disturbance is detected, the next accepted change must shift axis,
  cite fresh signal, or halt. Cosmetic / rustfmt / file-rotation /
  naming-cleanup / ledger-only changes do not satisfy the mode break.
  (See template's `### Same-family admissibility` section.)
- **Frontier anchor requirement:** every accepted change cites a live
  anchor (acceptance row / OPEN finding / failing trace / missing
  evaluator artifact). (See template's `### Frontier anchor
  requirement` section.)
- **Halt conditions:**
  > - No OPEN findings for N consecutive review rounds (default N=2).
  > - Scope drift detected (if manifest provided).
  > - All five homeostasis axes in balance and no intervention is
  >   available (frontier-exhausted equilibrium).
- **Quiet-signal checkpoint:** after N green iterations with no new
  failing trace / finding, run the outer channel or emit
  `stop-and-summarize`. Default N=3; override via `{{QUIET_SIGNAL_N}}`.

### 6.5. Encode ramp if needed

If ramp mode is active:

- Prepend a **ramp preamble** to the derived prompt naming the missing
  stages, the ramp-axes (harness completeness, signal discrimination,
  trace legibility), and the ramp exit criterion (stages 1–5 complete).
- In the homeostasis block, add ramp-axes above main-loop axes. Note
  that main-loop axes are advisory until ramp exits.
- State explicitly: ramp is deep, multi-iteration work. A single stage
  may span a PR or a branch. This is not failure.
- Include an explicit ramp exit signal: when stages 1–5 are complete,
  the loop emits `ramp-complete` and the next iteration switches to
  main-loop homeostasis.

If ramp is encoded in the motive (row 1 is ramp-shaped):

- Label the first row/phase as `ramp` in the rationale.
- Do not duplicate with a preamble; the motive's own row is the ramp.
- Ensure the ramp exit criterion maps to that row's completion.

### 7. Overlay review-closure if applicable

If step 4 classified closure mode, append the precedence + anti-collapse
rules from [`references/review-closure-overlay.md`](references/review-closure-overlay.md).

### 8. Emit

Return:

- **The final prompt** — one document, self-contained. The loop agent
  reads only this.
- **A one-paragraph rationale** — evaluator tier, ramp mode (not in
  ramp / in ramp with stages X/Y/Z missing / ramp encoded in row N),
  reward-channel classification, closure-overlay applied or not.
- **Optional artifact contract** — if the repo lacks a clean harness
  layout, suggest (do not silently invent):
  ```
  scripts/validate.sh        # cheap inner channel
  scripts/eval.sh            # expensive outer channel
  benchmarks/search_set/     # used during search
  benchmarks/holdout/        # not used for choosing changes
  docs/golden_principles.md  # invariants the agent preserves
  ```
  Call out how this sits relative to existing repo conventions.

## Failure modes

The derived prompt guards against:

- **Goodharting** — primary metric rises while real capability rots.
  Mitigation: Pareto-frontier metrics; discriminative search set; T6
  holdout.
- **Oracle fetishism** — loop keeps improving tooling without cashing out
  into product improvement. Mitigation: once the evaluator is
  trustworthy, cash it out; intervention-diversity axis catches same-shape
  runs.
- **Status theater** — summaries and plans in place of committed work.
  Mitigation: prohibition + "traces are truth, notes are memory."
- **Self-authored closure** — iteration marks its own change closed; halt
  fires prematurely. Mitigation: FIXED ≠ CLOSED + external confirmation.
- **Scope drift** — loop improves adjacent surfaces. Mitigation:
  scope-manifest halt if provided.
- **Mode lock** — same work shape repeats when another channel is the
  better move. Mitigation: intervention-diversity axis + homeostasis
  diagnosis.

### Ramp-specific failure modes

- **Over-ramping** — elaborate harness when a smoke test would suffice.
  Detect: ramp stage 7 or 8 started before stage 4 exit condition met.
- **Premature optimization through ramp** — ramping to stage 9 while
  product can't be exercised end-to-end.
- **Ramp scope creep** — ramp never exits. Detect: stage 4 re-opened
  repeatedly without product changes that justify it.
- **Ramp bypass** — jumping to product work on whatever signal happens
  to exist. Detect: product commits landing while stage 4 exit unmet.
- **Ramp theater** — stage artifacts that look like infrastructure but
  don't sharpen signal. Detect: stage 4 exit claimed, false-green rate
  unchanged.

Each failure mode is a specific axis disturbance the homeostasis frame
should catch. The list is the diagnostic glossary for what imbalance
looks like in practice.

## Diagnostic mode

Invoked to diagnose a drifting loop (not author a new prompt):

- Walk steps 1, 2, 2.5, 4 against the current state.
- Compare recent work shapes against the failure modes.
- Name which axis is disturbed or which invariant is missing.
- Propose a prompt mutation (inline edit, not rewrite) that closes the
  gap.
- Record the diagnosis in the loop's ledger.

## References

- [`references/evaluator-maturity.md`](references/evaluator-maturity.md) —
  T0–T6 tier ladder, ramp stages 0–9, tier-to-stage cross-reference,
  common failure modes.
- [`references/review-closure-overlay.md`](references/review-closure-overlay.md)
  — precedence and anti-collapse rules for closure mode.
- [`references/frontier-prompt-template.md`](references/frontier-prompt-template.md)
  — output template. The per-iteration playbook lives here and gets
  populated at derivation time.
- [`references/braid-case-study.md`](references/braid-case-study.md) —
  concrete example that produced this skill, including the greedy
  proof-promotion failure mode.
