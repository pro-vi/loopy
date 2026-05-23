---
name: goal-loop
description: "Derive a repo-specific prompt for a terminal / oracle-shaped loop: finite acceptance criteria, per-criterion verifiers, one final-verify, halt when all pass without weakening the criteria."
---

# Goal Loop

A **terminal closing loop**. `goal-loop` closes a *finite list* of
acceptance criteria — every criterion has a verifier, and the run is done
only when one **final-verify** proves the whole inventory in the same
repo state.

Use it when
"done" means a known list of things must be true, simultaneously, with
machine-checkable proof. The per-iteration playbook lives in
[`references/goal-prompt-template.md`](references/goal-prompt-template.md);
the principles that keep the loop honest live in
[`references/oracle-principles.md`](references/oracle-principles.md).

`goal-loop` is the family-voice distillation of a heavyweight oracle-loop
framework — the wisdom (binary oracle, oracle independence, consumer-side,
anti-theater) without the syntax-heavy bureaucracy (multi-agent
`PROMPT_build.md` + `PROMPT_acceptance.md`, `loop.sh`, tagged lanes,
Phase + TICKET-ID decomposition). The runner contract — `/loop`, `/goal`,
external harness — handles iteration; the LLM handles execution. **No
bash, no taxonomy.**

## Core framing

A goal loop is **closure-shaped + terminal + multi-criterion**:

- A finite list of admissible criteria, each with a stable ID.
- One discriminative verifier per criterion (binary, independent — see
  [`references/oracle-principles.md`](references/oracle-principles.md)).
- A two-step proof: a per-criterion verifier pass marks
  `PASS_PENDING_FINAL`; only the **final-verify** (one command that proves
  the whole inventory in the same repo state) moves it to `PASS`.
- Halt when all criteria reach `PASS` → emit `criteria-met` →
  `stop-and-summarize`. Partial completion is `partial-deadlock`, never
  `criteria-met`.

Three concepts make goal-loop terminal where the other loops stay
open-ended:

1. **Acceptance inventory** — the frozen criteria list with stable IDs.
2. **Final-verify** — one command that proves every criterion in the same
   repo state, after each per-criterion verifier has passed individually.
3. **Oracle-drift guard** — criteria are frozen; the loop must not weaken
   them to make itself pass.

A single-criterion run is fine and lightweight — a finite inventory of one
covers the "I found one bug, close it" case without needing a separate
loop type.

Valid progress: any small reversible change that does at least one of:

1. moves an `OPEN` criterion toward `PASS` without weakening it,
2. repairs or encodes a strictness-preserving verifier for a frozen
   criterion,
3. preserves passing criteria while unblocking a dependent one,
4. sharpens failing evidence for a `STUCK` criterion so the next move is
   cheaper.

The target is the inventory. The oracle is the verifier matrix. The run is
complete only when the final-verify passes.

## When to invoke

The user or repo gives a *finite contract*:

- "implement this spec"
- "make these acceptance criteria pass"
- "close this issue's checklist"
- "make this design blueprint true"
- "make this suite pass without changing expected behavior"
- "rerun this previously-passing goal after a regression"

Architectural design blueprints (e.g. an `/architect`-style document with
unit IDs + test scenarios + a decisive choice) are first-class criteria
sources — their unit IDs lift in as criterion IDs, test scenarios lift in
as pass/fail evidence, the decisive choice becomes the authority.

## Do NOT invoke when

- The target is a quality frontier ("better", "more robust", "higher
  score") with no terminal pass line → `frontier-loop`.
- The job is product-promise discovery / verification before a finite
  target exists → `story-loop`.
- Target / artifact / evaluator is undefined → `greenfield-loop`.
- Criteria are subjective and cannot be made machine-checkable without
  inventing intent → route away.
- The loop would have to weaken the test / spec / criterion to pass —
  do not emit a goal prompt; diagnose oracle drift.

## What this skill produces

Three files in `loop/` (unless the repo has a stronger convention):

- **`loop/PROMPT.md`** — the runner-invoked terminal prompt.
- **`loop/ACCEPTANCE.md`** — the frozen criteria inventory (live anchor
  set). The loop mutates only `status` and `last_verification` fields.
- **`loop/STATE.md`** — goal version, iteration counter, current
  criterion, stuck counters, inline Oracle Change Notes, `Next action:
  HALT` hatch.

Optional:

- `loop/VERIFY.md` — final-verify transcript; written on terminal
  completion.
- `loop/PROMPT_verify.md` — only when oracle independence requires a
  separate verifier agent (see `references/oracle-principles.md`); rare,
  high-stakes only.

## Frontloading discipline

Loops fail when they self-terminate blocked on a decision the user could
have made before the loop fired. The derivation step's job is to
**frontload every uncertainty** so iteration runs through the night
without blocking.

At derivation time, for every thing the loop might need (criteria source,
verifier commands, final-verify, scope, fixtures, …), do one of:

1. **Resolve it now.** If the host has an AskUserQuestion-style tool
   (Claude Code, similar), use it. Otherwise print the uncertainty
   prominently in the derivation response so the user can answer before
   launching.
2. **Default + Alignment Review.** Pick the smallest reversible default
   and record an Alignment Review (problem · options · chosen contract ·
   rollback trigger).
3. **Mark as escalate candidate.** Only for the truly irreversible.

Anything left over is a **derivation gap**. The emitted prompt's
halt-cause classifier flags `derivation-gap` halts so the next derivation
pass closes them.

## Derivation procedure

### Pre-flight: Frontload audit

Walk this checklist. For each item: **resolve** (AskUserQuestion if
available, else print prominently), **default + Alignment Review**, or
**escalate-mark**. Record in `loop/STATE.md` under `frontload:` and
prepend a frontload preamble to the emitted prompt.

- **Motive** — one-sentence terminal goal.
- **Criteria source** — exact file path, issue URL, design blueprint
  (`/architect`-style), test selector list, or migration plan. Tests
  are verifier surfaces, not authoritative intent unless the user names
  them as such.
- **Authority order** — which source wins when criteria conflict.
- **Acceptance inventory** — finite list with stable IDs (`AC-XXX`).
- **Goal version** — fingerprint of inventory + authority + final-verify.
- **Per-criterion verifier** — exact command + concrete pass evidence.
- **Final-verify** — one command for the whole inventory.
- **Cheap inner channel** — lint / typecheck / smoke; run before each
  criterion verifier.
- **Expected-fail evidence** per criterion — known red, mutation
  sentinel, or `none-known` with justification.
- **Dependency topology** — DAG; cycles are derivation gaps.
- **Scope manifest** — allowed / forbidden edit globs (binary in/out, not
  percentage).
- **Known false-green zones** — snapshots, skipped tests, weak selectors.
- **Forbidden shortcuts** — `--no-verify`, test deletion, snapshot
  refresh, skipped / xfailed criteria.
- **Partial-completion policy** — `{{STUCK_ATTEMPT_N}}`, default 3.
- **Oracle independence** — does this goal need a separate verifier agent?
  (Default no; see `references/oracle-principles.md` for when yes.)
- **Regression mode** — new run, or rerun of a previously-passed goal
  version?
- **Artifact locations** — concrete paths.

A derivation that doesn't close all of these — at least to defaults —
emits a frontload preamble naming the open gaps so the user can decide
before sleeping.

### 1. Locate the contract

Find the *authority source*: spec doc, issue checklist, design blueprint
(`/architect`-style), PR review, test suite to pass, migration plan.
Criteria come from **outside the loop**; a test the loop authors is a
*verifier* for a frozen criterion, never the source of intent.

If no authoritative finite contract exists, the work is not goal-shaped.
Decline and route.

### 2. Build the acceptance inventory

Emit `loop/ACCEPTANCE.md`. Each criterion has the minimum shape:

```yaml
- id: AC-001
  statement: "<user-visible requirement, not implementation>"
  source: "<doc/issue/test selector + revision/hash>"
  authority: user-current | spec-current | issue-current | architect-blueprint | test-existing | inferred-with-review
  verifier: "<exact command>"
  pass_evidence: "<stdout line | JSON field | test selector | artifact assertion | DOM check | threshold>"
  fail_evidence: "<known red, mutation sentinel, or none-known + justification>"
  status: OPEN
  depends_on: []
  reopen_condition: "<what would reopen this if it passes>"
```

Inventory is **frozen** for the run. The loop may update `status` and add
a `last_verification` field — never edit `statement` or `source`.
Verifier changes go through Oracle Change Notes (template's
`## Oracle-drift guard`).

If two authoritative-current sources conflict, mark the criterion
`QUARANTINED` and emit `escalate: source-conflict`. Do not pick the
easier reading.

### 3. Define verifiers + final-verify

For each criterion, write a verifier command and the exact pass evidence.
Apply oracle principles (see `references/oracle-principles.md`):

- **Binary** — pass/fail; never subjective.
- **Independent** — a loop-authored verifier must first fail (mutation,
  sentinel, known-bad fixture). If it cannot fail, it cannot prove.
- **Consumer-side** — *"if this passes, does the user have a working
  feature?"* If the answer requires inference, the verifier is wrong.

The **final-verify** is one command that proves every criterion in the
same repo state. Examples: existing CI command if it covers the
inventory, `scripts/goal-verify.sh` as a bespoke matrix, `pytest
tests/acceptance/` when criteria map to acceptance tests.

If a final-verify doesn't exist and criteria are frozen, the loop may
build one — but only as composition of the per-criterion verifiers.
Building a final-verify that requires choosing what "good" means is
frontier work; route away.

### 4. Establish dependency topology (if any)

Criteria are independent by default. Add `depends_on` only when *proof*
requires order (not implementation taste). The graph must be acyclic. A
passing criterion is a regression guard for dependent edits.

### 5. Assemble the prompt

Start from
[`references/goal-prompt-template.md`](references/goal-prompt-template.md).
Fill `{{MOTIVE}}`, `{{GOAL_VERSION}}`, the Runner contract + Judgment
default + Frontloading discipline blocks (sibling skills share these —
keep in sync), `{{CHEAP_CHANNEL}}`, `{{FINAL_VERIFY}}`, `{{TOPOLOGY}}`,
`{{STUCK_ATTEMPT_N}}`, `{{FORBIDDEN_SHORTCUTS}}`,
`{{ARTIFACT_LOCATIONS}}`, `{{REPO_SPECIFIC_OVERLAY}}`.

### 6. Emit

Return:

- `loop/PROMPT.md` — the filled-in terminal prompt.
- `loop/ACCEPTANCE.md` — initial inventory, or unchanged if already
  correct for this goal version.
- `loop/STATE.md` — goal version, mode (`new` / `regression`), iteration
  0, current criterion `none`, last action, next action, `Next action:
  HALT` hatch.
- A short rationale: criteria count, source authority, verifier coverage,
  dependency topology, partial-completion threshold, regression mode if
  applicable.

If the work is not goal-shaped, do **not** emit a goal prompt as if it
were safe. Explain why and route.

## Failure modes the prompt guards against

- **Oracle drift** — the loop weakens criteria / verifiers / expected
  evidence to pass. Mitigation: frozen inventory + Oracle Change Notes
  + final-verify.
- **Verification theater** — same entity producing + verifying.
  Mitigation: oracle independence (principle 2); optional separate
  verifier agent for high stakes.
- **Pass theater** — a single verifier green claimed as completion.
  Mitigation: `criteria-met` requires the final-verify to prove the whole
  inventory in one repo state (`PASS_PENDING_FINAL` → `PASS`).
- **Partial-victory laundering** — "7 of 10 is basically done."
  Mitigation: partial completion is `partial-deadlock`, never
  `criteria-met`.
- **Verifier vanity** — tests exist but don't distinguish wrong
  behavior. Mitigation: `fail_evidence` (mutation / sentinel / known red).
- **Scope escape** — editing adjacent surfaces to make proof easier.
  Mitigation: scope manifest (binary in/out, not percentage).
- **Frontier masquerade** — a moving target forced into a terminal
  checklist. Mitigation: `wrong-loop` halt-cause with reroute targets.

## Diagnostic mode

Invoked to diagnose a drifting goal loop (not author a new prompt):

1. Read `loop/PROMPT.md`, `loop/ACCEPTANCE.md`, `loop/STATE.md`, latest
   verification artifacts, recent diffs.
2. Check the inventory still matches its recorded provenance and goal
   version.
3. Identify the dominant failure: oracle drift · verification theater ·
   pass theater · partial-victory laundering · verifier vanity · scope
   escape · wrong-loop drift.
4. Emit a minimal PROMPT.md mutation that closes the gap — not a full
   rewrite.
5. Write a ⚠️ block to `loop/STATE.md` for the next iteration.

## References

- [`references/goal-prompt-template.md`](references/goal-prompt-template.md)
  — the output template. The per-iteration playbook lives here and gets
  populated at derivation time.
- [`references/oracle-principles.md`](references/oracle-principles.md) —
  the four oracle principles + honest-verifier invariants + anti-patterns
  that keep the loop honest.
