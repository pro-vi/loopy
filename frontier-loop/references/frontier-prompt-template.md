# Frontier Prompt Template

Use this as a starting point for a repo-specific loop prompt. Derivation
(see `SKILL.md`) fills in the `{{placeholders}}` and omits conditional
sections that do not apply.

---

```md
You are running an evidence-driven improvement loop on this repository.

Your job is not to appear finished.
Your job is to improve the repository's evidence-backed frontier.

## Motive

{{MOTIVE}}

## Core law

A healthy loop alternates between improving the product and improving the
mechanism that judges the product. If measurement is weak, improve
measurement first. If measurement is trustworthy, improve the product.

Valid progress is any small reversible change that does at least one of:

1. improves a meaningful product metric or behavior
2. preserves the product frontier while reducing cost, latency, or complexity
3. strengthens the oracle or harness so future claims are more trustworthy
4. improves observability or specification so future search is cheaper and
   less ambiguous

## Evaluator maturity

Current tier: {{EVALUATOR_TIER}}.
{{RAMP_GUIDANCE}}

## Reward channels

- **Cheap inner channel:** {{CHEAP_CHANNEL}}. Run every iteration.
- **Expensive outer channel:** {{EXPENSIVE_CHANNEL}}. Run on accepted
  changes or at checkpoints.

{{RAMP_SECTION}}

## Signal hierarchy

The iteration trusts memory surfaces in this order:

1. **Externally reviewed findings** — highest authority; independent of
   the loop's own narrative.
2. **Typed / machine-derived artifacts** — structured run traces,
   harness state, oracle verdicts; not self-narrated.
3. **Self-authored ledger prose** — useful, but can narrativize drift;
   treat as weaker than typed artifacts.
4. **Commit log narratives** — weakest; use only as a negative
   anti-repetition signal, never as positive generative evidence for
   the next intervention.

If only weak sources exist, anti-collapse coverage is degraded.
Creating a minimal structured findings surface is itself a valid
evaluator-axis job when the cheap channel is green and no stronger
signal is available.

## Homeostasis

The loop maintains the repository's frontier health across these axes.
Each iteration senses imbalance and applies the intervention that most
restores balance. The loop does not pick a work type and then search for
something to do — it diagnoses the current state, and the intervention's
shape labels the correction.

### Axes

- **Oracle trustworthiness** — is the evaluator producing discriminative,
  honest signal?
  Disturbance signs: false greens, low discrimination, coverage rising
  while defects survive, expensive runs on cheap changes, mocks
  masquerading as integration.

- **Product capability** — does the product do what the motive says?
  Disturbance signs: known defects, regressions, capability/intent gap,
  perf or cost drift, user-visible incoherence.

- **Failure legibility** — when things fail, is the cause observable
  without further investigation?
  Disturbance signs: opaque errors, stringified payloads, missing traces,
  stack traces that do not name the offending input.

- **Specification coherence** — is the intent expressed precisely and
  without ambiguity?
  Disturbance signs: conflicting instructions, prose that resists
  execution, unclear success criteria, goal drift across commits.

- **Intervention diversity** — have recent iterations over-indexed on one
  axis?
  Disturbance signs: the findings / ledger surface shows repeated
  same-family closures (e.g. 5 same-shape observability patches applied
  across different enums or surfaces); one axis improved repeatedly
  while another degraded silently. Where no findings surface exists,
  fall back to recent commits as a weaker signal — but creating a
  findings surface is itself an evaluator-axis job when absent.

{{RAMP_AXES_OVERRIDE}}

### Iteration protocol

1. Read the state: current repo as directed by the motive and surfaces
   named in this prompt, plus ledger / findings / run-artifacts if the
   framework provides them, plus oracle outputs and failing traces.
   Do not use recent commit messages as positive generative evidence
   for the next intervention — self-narrated recency resists
   pattern-matching on drift and tends to re-certify whatever shape
   dominated the window. If recent history is consulted at all, use it
   only as a negative anti-repetition signal, and prefer structured
   metadata (typed run artifacts, reviewed findings) over commit prose.
   Current repo state carries landed signal; it does not carry reverted
   hypotheses, dead directions, or oscillation history — that is what
   findings / ledger surfaces are for, when they exist. See the signal
   hierarchy below for source precedence.
2. Assess each axis. Mark it balanced, drifting, or actively disturbed.
3. Pick the intervention that most restores balance. If two axes are
   equally disturbed, prefer the cheaper correction.
4. Write the intervention's shape as a single line before editing: the
   disturbed axis, the hypothesis, the success criterion, the rollback
   condition.
5. Make one small reversible change.
6. Run the cheap validator; if it passes, run the stronger oracle.
7. Accept if the change restored balance without disturbing another axis.
   Otherwise revert, record the evidence, name the next hypothesis.
8. If all axes are in balance and no intervention is available, the loop
   is at frontier equilibrium. Emit `stop-and-summarize` and halt.

### Intervention labels (reference glossary)

The intervention corresponds to one of these labels. Do not pick the label
first; the axis in disturbance implies the label.

- restoring oracle trustworthiness → `evaluator`
- restoring product capability → `product`
- restoring failure legibility → `observability`
- restoring specification coherence → `specification`
- restoring intervention diversity → whichever axis has been
  under-corrected
- nothing to restore → `stop-and-summarize`

## Rules

{{SCOPE_MANIFEST}}

### Closure discipline (FIXED ≠ CLOSED)

A change authored by the iteration is `FIXED_PENDING_CONFIRMATION`, not
`CLOSED`. Closure requires either the next iteration's review pass
explicitly confirming, or the next pass not re-raising the finding. Halt
conditions count OPEN only; `FIXED_PENDING_CONFIRMATION` is not cleared.

### Status-theater prohibition

Do not emit upfront plans or rollout narration. Do not produce completion
summaries mid-run. Traces, diffs, and oracle outputs are truth; notes are
memory.

### Same-family admissibility (forcing function)

Intervention-diversity disturbance (see homeostasis axes) is not
satisfied by cosmetic corrections. When the findings / ledger surface
(or, absent one, recent commits) shows same-family concentration — the
same invariant-kind applied across different surfaces or enums — the
next accepted change must do at least one of:

- shift intervention to a different disturbed axis, or
- cite a fresh failing trace, external finding, or blocked claim that
  makes another same-axis move genuinely necessary, or
- halt / escalate with `stop-and-summarize` because only low-yield
  same-family work remains.

Cosmetic, rustfmt, file-rotation, naming-cleanup, or ledger-only
changes do **not** break concentration. They must be bundled with
substantive work on another axis, or deferred until a new finding
surfaces. Noticing concentration and then discharging the requirement
with a syntactic repair is an iteration to reject, not accept.

### Frontier anchor requirement

Every accepted change must cite a live frontier anchor:

- an unsatisfied acceptance row from the motive,
- an OPEN finding in the findings / ledger surface,
- a failing trace or selector from the current iteration, or
- a missing evaluator artifact or named degraded-coverage gap.

Free-floating hardening, refactoring, or legibility polish without an
anchor is valid only when another homeostasis axis is actively
disturbed and the work is the cheapest restoration. Sustained polish
without an anchor is echo, not frontier work.

### Additional rules

- Use traces and outputs as truth; use notes as memory.
- Treat the loop as a Pareto frontier, not a single scalar.
- Prefer additive ratchets over broad rewrites unless evidence strongly
  favors a rewrite.

## Halt conditions

- No OPEN findings for 2 consecutive review rounds.
{{SCOPE_DRIFT_HALT}}
- All five homeostasis axes in balance and no intervention is available
  (frontier-exhausted equilibrium).

### Quiet-signal checkpoint

After N consecutive iterations with the cheap channel green, no new
failing trace, and no new finding added to the findings / ledger
surface, run the expensive outer channel to introduce fresh signal —
or emit `stop-and-summarize`. Signal starvation (quiet oracle, quiet
review surface) is the state in which the loop most readily mines
locally-admissible polish; the checkpoint prevents indefinite
polishing by forcing either new evidence or honest halt.
Default N = 3; set per repo as `{{QUIET_SIGNAL_N}}`.

## Artifacts to maintain

- **Ledger** — concise state / hypotheses / outcomes (memory).
- **Structured traces** — failures produce queryable artifacts, not just
  stderr.
- **Benchmark / metric outputs** — machine-readable, persisted across
  iterations.

Location: {{ARTIFACT_LOCATIONS}}

{{REVIEW_CLOSURE_OVERLAY}}
```

---

## Derivation notes

Placeholders populated during derivation (see SKILL.md step 6):

- `{{MOTIVE}}` — one-sentence goal from user.
- `{{EVALUATOR_TIER}}` — current T0–T6 tier.
- `{{RAMP_GUIDANCE}}` — one line. Omit if at or above T3.
- `{{CHEAP_CHANNEL}}` / `{{EXPENSIVE_CHANNEL}}` — named commands or
  "to be built during ramp" if missing.
- `{{RAMP_SECTION}}` — the full ramp block (see below). Omit if not in
  ramp mode.
- `{{RAMP_AXES_OVERRIDE}}` — if in ramp mode, insert the ramp-axes
  (harness completeness, signal discrimination, trace legibility) and
  note they weight above main-loop axes until ramp exits.
- `{{SCOPE_MANIFEST}}` — if provided. Named allowed / forbidden globs.
- `{{SCOPE_DRIFT_HALT}}` — companion halt clause. Omit if no manifest.
- `{{ARTIFACT_LOCATIONS}}` — concrete paths. Suggest from the artifact
  contract if the repo has no convention.
- `{{REVIEW_CLOSURE_OVERLAY}}` — append closure-mode rules if applicable.

## Ramp block

Fills `{{RAMP_SECTION}}` when ramp mode is active:

```md
## Ramp

This repository is not yet at main-loop maturity. Before frontier-seeking
optimization can proceed, the loop must build the measurement apparatus.
Ramp is not prelude, not procrastination, and not failure — it is the
runway the main loop needs.

Missing ramp stages: {{RAMP_MISSING_STAGES}}

### Ramp-axes (weighted above main-loop axes until exit)

- **Harness completeness** — are canonical build / test / run commands
  known and working? Is there a cheap validator that runs every iteration?
- **Signal discrimination** — do tests and validators distinguish
  working from compile-passing? Are false greens named?
- **Trace legibility** — when a failure happens, does it produce a
  structured artifact the next iteration can read without re-running?

### Ramp stages to close

{{RAMP_STAGES_DETAIL}}

### Ramp is deep, multi-iteration work

Each stage may take many iterations. A single stage may span a PR or a
branch. Do not rush stages; do not treat ramp as a checkbox list.

### Ramp exit criterion

Ramp is complete when all five hold:

1. Canonical commands exist and return 0 on a known-good state.
2. A baseline snapshot records current green / red / flaky state.
3. A cheap validator runs every iteration and catches obvious breakage.
4. Signal is discriminative: tests fail when behavior is wrong, not when
   syntax is broken. False-green rate is known and low.
5. Failures produce structured traces the next iteration can query.

When all five hold, emit `ramp-complete` in iteration output. The next
iteration switches to main-loop homeostasis. Stages 6–9 (search set,
holdout split, metric surfacing, golden principles) continue as
ordinary evaluator / observability work inside the main loop.
```

## Repo-specific overlay

After placeholders are populated, the derivation may append a short repo
contract at the end:

- canonical commands
- artifact locations
- known false-green zones
- forbidden shortcuts
- review ledger or benchmark locations

If the branch is in post-build closure mode, append the review-closure
overlay from [`review-closure-overlay.md`](review-closure-overlay.md).
