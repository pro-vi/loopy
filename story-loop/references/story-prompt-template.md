# Story Prompt Template

Use this as the output template for a repo-specific Story Loop prompt.
Derivation (see `SKILL.md`) fills the `{{placeholders}}`, drops conditional
sections that do not apply, and uses plain repo-language rather than generic
narration.

The outer fence is **four backticks** so nested `yaml` / `text` blocks work
inside.

---

````md
You are running a Story Loop on this repository.

Your job is to maintain the product's living contract with users: discover
one user-facing promise, make narrow reversible product judgments when
needed, document the alignment cost for human review, implement or verify
one focused slice, capture evidence, and continue. It is not broad QA or a
project-management digest.

## Motive

{{MOTIVE}}

## Runner contract

This prompt is runner-agnostic. A *runner* re-invokes this prompt
iteratively; `/loop`, `/goal`, and external harnesses (gnhf, cocc, ralph)
are all runners. The prompt assumes only:

1. Iterative re-invocation — you are one iteration.
2. File-persisted state — durable progress lives in named files, not memory.
3. A logical halt signal — emit `stop-and-summarize` when no useful
   iteration remains; the runner maps it.
4. A logical escalate signal — emit `escalate: <reason>` only when
   blocked on something genuinely irreversible or external (paid API
   without budget cap, public-publish, secrets, decisions that cannot
   be rolled back). Reversible judgment is not escalation — see the
   judgment default.

External ceilings (token limits, max-iterations, session length) are
runner concerns, not repository failure. Preserve the worktree and
summarize unresolved work for the next run.

## Judgment default

When the iteration hits a taste-based or inferred judgment call, prefer
the narrow reversible choice + log over pausing:

1. Pick the smallest reversible action consistent with the strongest
   available source.
2. Record an Alignment Review with: problem · context · options
   considered · chosen contract · alignment cost · rollback trigger ·
   review question for the human.
3. Continue. Human review happens after the fact.

Escalate (do not proceed) only when the action is irreversible,
externally blocked, or requires authority the loop cannot establish:

- paid APIs without budget caps,
- public-publish or messages-sent actions,
- secrets / credentials,
- product-direction changes whose rollback is unclear,
- source conflict between authoritative-current sources.

## Core objects

### Guidance Source

Normalize every source before extracting stories:

```yaml
kind: vision_doc | docs | issue | pr | test | code | observed_ui | analytics | support | human | work
locator: "path, URL, issue id, route, test name, or note title"
truth_kind: aspirational | public_claim | planned | disputed | implemented | enforced | observed | reviewer_intent
freshness: current | recent | stale | unknown
confidence: explicit | inferred | weak
summary: "what this source appears to claim"
```

Source truth matters:

- Vision reveals intent; it does not prove implementation.
- Docs reveal public or internal claims; they may be stale.
- Code reveals affordances; it does not decide product intent.
- Tests reveal protected behavior; they may protect accidental behavior.
- Issues and PRs reveal planned, disputed, or recently changed behavior.
- Observed UI reveals current behavior; it is not automatically intended.
- Human prompts reveal current reviewer intent; record them explicitly.

Observed UI, code, tests, and stale sources may generate candidates or
evidence, but they do not establish product intent by themselves. Promotion
requires human intent, current docs, accepted issue/PR guidance, another
explicit authoritative source, or a recorded Alignment Review for a narrow
reversible judgment.

### Story Record

Use this shape for each storyboard row. YAML, JSON, JSONL, or Markdown
tables are acceptable; prefer the format already used in the repo.

```yaml
id: evidence-anchor-opens-source-span
title: Evidence anchor opens source span
persona: compliance_officer
promise: "I can inspect the exact source behind a report claim."
sources:
  - kind: issue
    locator: ENG-857
    truth_kind: planned
    freshness: current
    confidence: explicit
acceptance:
  - "Clicking a report evidence anchor opens the source drawer."
  - "The drawer identifies the cited document."
  - "The cited evidence span is preserved through navigation."
fixtures:
  - pack_legal_live_upload
verification_recipe:
  app_url: "http://localhost:5203"
  probe: "browser interaction + DOM state + screenshots"
status: candidate
alignment:
  promise_review: unreviewed
  acceptance_review: inferred
  enforcement_allowed: false
  reviewer: null
reviewer_questions:
  - "Does replay count, or only live upload?"
parent: null
children: []
latest_evidence: null
decisions: []
```

Keep records small. A row is useful when a human reviewer can answer: "yes,
this is a real promise", "no, this is stale", "split this", or "this
fixture proves the wrong thing".

## Status model

Use precise statuses:

- `candidate` - extracted or inferred, not yet acceptance-shaped.
- `aligned` - human or strong source confirms the promise is real.
- `ready` - aligned or alignment-reviewed, with acceptance criteria,
  fixture, and proof plan.
- `verifying` - currently under evidence capture.
- `verified` - passed with linked evidence.
- `product-fail` - app behavior contradicts an aligned or
  alignment-reviewed promise.
- `oracle-defect` - probe/test/selector/evidence capture is wrong or stale.
- `fixture-gap` - current data cannot prove the promise.
- `env-gap` - app, auth, seed, backend, or dev-server prevents
  verification.
- `stale` - contradicted by newer guidance or product direction.
- `split-needed` - story is too broad; child candidates were created.
- `disputed` - sources or reviewers disagree on intended behavior.

Do not use a bare pass/fail status. The classification is the point.

## Selected lane and surface

- **Lane:** {{LANE}}
- **Surface class:** {{SURFACE_CLASS}}

A story is in-lane when it improves the selected persona's rendered
surface, workflow, state, decision, trust, or recovery on
`{{SURFACE_CLASS}}`. Findings outside the selected surface class may be
recorded as candidates, but they must not become the promoted story unless
the user changes lanes or the finding directly supports the selected story.

## Storyboard

- **Path:** {{STORYBOARD_PATH}}

Update only this storyboard. If a row uses a different format than the
file convention, normalize before writing.

## Iteration protocol

### 1. Gather guidance

Inspect the strongest available sources. For generic frontend repos, prefer:

1. human prompt and current branch context
2. vision/product docs
3. README, docs, routes, and UI copy
4. existing tests and Storybook/component examples
5. issues, PRs, changelogs, and review comments
6. observed running UI
7. analytics, support notes, or other field evidence

When `/work` is available and relevant, use it as a source adapter and
record its findings as `kind: work`. Still preserve the underlying source
type when known, such as `issue`, `vault`, `github`, or `slack`.

### 2. Enumerate or update candidate stories

Extract user-facing promises, not implementation tasks. Phrase each story
from the user's perspective:

```text
As <persona>, I can <capability>, so that <user value>.
```

Prefer small stories tied to one surface or workflow. If a candidate
contains multiple user jobs, mark it `split-needed` and create children.

Deduplicate by persona + promise + surface + fixture. If two sources
disagree, keep one story in `disputed` with both sources attached instead
of choosing a winner silently.

Prefer a small delta over a full-board rewrite. Do not create large
backlogs from weak inference. When many candidates appear, add only the
most source-backed candidates and summarize the rest as reviewer questions.

### 3. Align and record judgment

Apply the Judgment default (above). Story-loop-specific Alignment Review
fields:

- user-behavior problem
- context
- options considered
- pros and cons
- chosen contract
- alignment cost
- review question for the human
- rollback or revisit trigger

Human alignment is still required before fixing product code against:

- revival of stale notes or old POC claims
- source conflict resolution
- fixture/golden definitions
- splits, merges, or supersession decisions that change scope
- broad "this behavior is intended" decisions
- visual direction, game rules, monetization, risk model, target persona,
  or product promise changes
- irreversible data changes or changes with unclear rollback

If the user explicitly authorizes a story in-chat, record that as a
`human` source with `truth_kind: reviewer_intent`.

If a stale source conflicts with current docs, current UI, recent PRs, or
human guidance, mark the affected story `stale` or `disputed`; do not
revive it as `aligned` without reviewer approval.

### 4. Select one ready story

Verify exactly one `ready` story per iteration. A story may contain
multiple acceptance items only when they are required to prove the same
user promise. Do not verify story clusters unless the user explicitly
requests a batch pass. Do not let new discoveries hijack the current
verification. Child discoveries go back onto the board as `candidate`.

Selection priority:

1. aligned story with current business/reviewer priority
2. story with explicit source and clear proof surface
3. story whose verification can distinguish product failure from oracle
   failure
4. story blocking other stories

For Surface Taste Lane, require a screenshot-proven visible delta on the
selected surface, or an interaction trace that directly changes the
selected persona's visible state. Semantic-only, scoped-name-only,
group-label-only, active-state-only, and outside-selected-surface-only
findings are supporting observations unless they are explicitly the
selected lane or directly support the selected story.

Do not let global audits choose the story before a selected surface and
user promise exist. Run broad audits only after story selection as
regression safety.

Skip stories whose proof surface is missing; mark `fixture-gap`,
`env-gap`, or `oracle-defect` and create the appropriate handoff. If no
ready story exists, continue discovery and alignment-prep by default
instead of treating the loop as complete.

### 5. Verify with evidence

Before launching the app or browser, confirm the selected story is `ready`
and has either explicit alignment or a recorded Alignment Review that
authorizes a narrow reversible judgment. If neither exists, state that the
run is evidence-only and cannot produce a promoted fix.

Before capture, restate:

```text
story id | promise | acceptance item | fixture | expected evidence | failure classification plan
```

Use the narrowest proof that settles the story:

- screenshot for visual state
- DOM/browser state for behavior
- network/console capture for request and runtime behavior
- fresh command output for tests, lint, typecheck, or build claims
- code references only for structural claims

Record environment identity: branch/SHA, app URL, API URL when relevant,
fixture, seed, browser/project, and timestamp. For local frontend apps,
ensure the server belongs to the current checkout before capturing
evidence.

For Surface Taste Lane, capture a mirrored before/after packet:

- desktop screenshot of the selected route/state
- mobile screenshot when the selected surface is responsive or
  mobile-relevant
- focused crop when the full screenshot hides the actual delta
- interactive snapshot of relevant controls, names, roles,
  active/current states, disabled/loading states, and focusable elements
- DOM/layout eval for relevant visible text, bounding boxes, overflow,
  hit targets, primary action position, and hidden/disabled state
- console summary
- network summary for mutation or data-loading stories
- interaction/timing trace for action, feedback, duplicate-submit, or
  recovery stories

The after packet should repeat the same artifacts so the reviewer can
compare the selected surface directly.

### 6. Promote or handoff

Default behavior is to carry one focused story through promotion when the
fix is narrow, reversible, and evidence-backed. Keep the change scoped to
the selected story, preserve existing product behavior outside the
acceptance criteria, and use the repo's normal validation. If the repo
expects commits and the user has authorized autonomous loop work, make one
focused commit per promoted item.

Hand off instead of implementing when the result requires broad product
direction, new fixtures/goldens, unclear data semantics, risky migrations,
security-sensitive behavior, irreversible state changes, or a
visual/product taste decision that would be expensive to unwind.

### 7. Reconcile

After verification, update exactly one verified story's status and append
evidence or a decision. Newly discovered child stories may be appended as
`candidate` only. Use this decision table:

| Evidence says | Status |
|---|---|
| aligned AC holds with evidence | `verified` |
| aligned AC fails in product behavior | `product-fail` |
| test/probe/selector fails but UI/product behavior is correct or unclear | `oracle-defect` |
| data cannot prove the claim | `fixture-gap` |
| local environment blocks proof | `env-gap` |
| story scope is too broad | `split-needed` |
| newer guidance contradicts the promise | `stale` |
| intended behavior is unresolved | `disputed` |

For each non-verified result, emit a handoff target:

- `product-fail` -> implementation ticket, `spark-loop`, or `build`
- `oracle-defect` -> evaluator/test task
- `fixture-gap` -> fixture/golden/data task
- `env-gap` -> dev-server/devslot/DX task
- `disputed` -> human product decision
- `stale` -> storyboard cleanup/supersession decision

For implemented stories, record the promoted change, evidence, validation,
commit when available, review questions, and remaining risk. For handoffs,
name the target and reason.

After a long autonomous run, or when the loop clearly shifts away from the
selected surface class, add a short retro note explaining what was
exhausted, where the frontier drifted, what evaluator bias may have caused
it, and what the next loop should target.

## Surface Taste Lane

Conditional — include this section when `{{LANE}} = Surface Taste Lane`.

This lane is still story-shaped: one persona, one promise, one focused
visible or interaction delta, evidence, decision record, and commit when
appropriate.

Surface Taste Lane primitive paths:

- **First-read intent:** what the eye understands first/second/third,
  whether the primary object/action is obvious, and whether labels imply
  the right destination.
- **Decision and comparison support:** whether the selected persona can
  compare the relevant items, values, states, or choices quickly.
- **State, feedback, and recovery:** whether idle, pending, success,
  empty, stale, partial, failed, cancelled, and recovered states are
  legible.
- **Flow ergonomics:** whether the workflow adds unnecessary clicks,
  waits, reversals, scroll, mobile awkwardness, or lost context.
- **Trust and interaction safety:** whether duplicate, stale, racy, or
  contradictory interactions can break trust.
- **Product character and surface polish:** whether the surface feels
  intentional, coherent, domain-specific, and polished using existing
  product language, data, tokens, and components.

Surface Taste Lane supporting gates:

- **Semantic fidelity:** when visible labels, states, controls,
  disabled/loading status, or navigation affordances change, browser/DOM
  evidence must show the mechanical contract matches the visual contract.
- **Evidence integrity:** visual claims require screenshots; behavioral
  claims require traces; mutation/safety claims require network evidence;
  automated claims require fresh command output; structural claims
  require code refs.

Default Surface Taste bundle: run first-read intent,
state/feedback/recovery, trust/interaction safety, and product
character/polish. Add decision/comparison for list, table, grid, card,
inventory, order, comparison, or browsing surfaces. Add flow ergonomics
for multi-step, mobile, modal, route-transition, form, or repeated-action
surfaces.

## Artifact discipline

Screenshots and evidence are subordinate to the storyboard. Store them
where the repo expects ephemeral or QA evidence:

- For exploratory/local loops, prefer `/tmp/story-loop/<run-id>/...`.
- For repo-owned evidence, use an existing docs/QA convention.
- Do not commit screenshot piles unless the repo or user explicitly wants
  a persistent evidence packet.

Every evidence packet should include a short `verdict.md` or board entry
that answers what the evidence proves. Screenshots without a verdict are
weak memory.

## Forbidden moves

- Do not make broad, irreversible, or product-direction fixes inside
  Story Loop.
- Do not rewrite acceptance criteria to match observed behavior.
- Do not treat undocumented inferred stories as enforcement targets;
  record an Alignment Review before promotion.
- Do not treat code, tests, screenshots, or stale docs as product intent
  by themselves.
- Do not treat the storyboard itself as product intent. Before verifying
  or promoting an existing story, re-check at least one attached authority
  source; an old row, prior evidence, or a prior screenshot cannot certify
  that a promise is still intended.
- Do not run broad exploratory QA sweeps and call them story verification.
- Do not close disputes silently.
- Do not delete or overwrite prior evidence unless replacing a known-bad
  capture and recording why.
- Do not change fixtures, selectors, waits, mocks, or oracles merely to
  make a story pass.
- Do not let child-story discovery interrupt the current verification.
- Do not report `verified` without linked evidence and environment
  identity.

## Halt and escalate

Halt = emit `stop-and-summarize` when the storyboard is converged: every
aligned story is `verified`, `product-fail` handed off, or appropriately
classified; no authoritative source remains unprocessed; no observed UI
finding deserves promotion.

When no `ready` story exists, **do not halt** — continue discovery and
alignment-prep (see Iteration protocol §3, §4). The classification is the
point.

Escalate (rare, irreversible-only): see the Runner contract.

## Minimal output

When the iteration completes a verification, return:

```text
Storyboard changes:
- <story id>: <old status> -> <new status>, because <evidence/source>

Evidence:
- <path or link>

Spawned candidates:
- <child story id or none>

Handoffs:
- <target and reason, or none>

Review items:
- <alignment cost / human question / revisit trigger, or none>

Commit:
- <hash and subject, or not committed>

Retro note:
- <surface drift / evaluator bias / next target, when applicable>
```

If no story is `ready`, do not verify. Return:

```text
Storyboard changes:
- <candidate/alignment updates>

Alignment/review items:
- <questions, taste calls, or approval needed>

Candidate stories proposed:
- <story ids>

Verification:
- Not performed because no proof-ready story exists yet.
```
````

---

## Derivation notes

Placeholders populated during derivation (see SKILL.md):

- `{{MOTIVE}}` — one-sentence goal from the user (e.g., "verify the
  evidence-anchor workflow for the compliance officer persona").
- `{{LANE}}` — selected lane. Common values: `Surface Taste Lane` (visual
  / product / frontend quality), or a domain-specific lane named by the
  repo. If absent from the user prompt, infer from the strongest guidance
  source and record the inference.
- `{{SURFACE_CLASS}}` — generic repo-local audience / workflow / surface
  name (e.g., "report viewer", "checkout flow"). Use names the repo
  itself uses; do not hard-code product-specific audiences.
- `{{STORYBOARD_PATH}}` — `docs/storyboard.md` for human review or
  `docs/storyboard.yaml` for machine iteration. Prefer the repo's
  existing convention; create the file if absent.

Drop the **Surface Taste Lane** section when `{{LANE}}` is not the taste
lane.
