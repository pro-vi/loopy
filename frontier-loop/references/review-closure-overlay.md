# Review-Closure Overlay

Apply this overlay only when the branch is clearly in closure mode:

- the main architecture is already landed
- the next work comes from reviewed findings, ledgers, or exact probes
- the branch already has a meaningful explicit proof surface

## Goal

Convert reviewed uncertainty into durable guardrails without collapsing into paperwork.

## Precedence

Use this order:

1. real product bug
2. harness / oracle blind spot
3. coherent proof-promotion batch
4. ledger update

If a promotion exposes a real product flaw, fix the product first.

## Promotion Policy

Promote only slices that are:

1. reviewed
2. stable
3. operator-meaningful
4. cheap enough to keep in the default suite

Do not aim to "promote all tests."

Keep large aggregates, expensive exhaustive checks, and useful exact probes available as explicit selectors when that is the better tradeoff.

## Batched Work

Batch by:

- invariant family
- matrix row
- failure family
- operator surface

Avoid one-selector-at-a-time promotions when a coherent family is ready.

## Anti-Collapse Rules

- if the findings / ledger surface shows repeated same-family closures
  across recent iterations (same invariant-kind applied across different
  surfaces or enums — e.g. "add Display impl for enum X and swap
  `{:?}` → `{}`" across many enums), force a mode break even when each
  individual closure looks locally justified. A mode break is one of:
  axis shift to a different disturbed homeostasis axis, citation of a
  fresh failing trace / external finding that makes another same-axis
  move genuinely necessary, or deferral via `stop-and-summarize`.
  Cosmetic, rustfmt, file-rotation, naming-cleanup, or ledger-only
  changes do **not** satisfy a mode break — this is the specific
  failure shape observed in the braid gnhf iter-64 case study.
- if the next best move is another narrow ledger update rather than a
  product or evaluator intervention, pause and diagnose before repeating
- treat the ledger as an evidence sink, not the main artifact of progress

These rules key on the framework's findings / ledger surface, not on the
recent commit log. The iteration does not re-read its own narrative to
pattern-match on drift; the persisted findings do. Where no findings
surface exists, coverage is degraded — creating one is itself a valid
evaluator-axis intervention; until then, anti-collapse falls back to
the weaker commit-log signal.

## Typical Pattern

When promotion is the right move:

1. preserve the legacy exact or ignored selector if it is still useful
2. factor the assertion into a shared helper if needed
3. add the stable default-suite sibling or equivalent always-on path
4. validate both the default path and the legacy exact probe
5. update the ledger after the code and proof land
