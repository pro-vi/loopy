# halt-shape (axis primitive)

## Purpose

The **reopen policy** — once the loop stops, is that final, reopenable, or only
the human's call? Weight 3. Distinct from `convergence-shape`, which names the
*stop signal*; halt-shape names what happens *after* the stop. A composed
prompt names both.

## Values

- `terminal` — halt is final for this goal version; reopens only via an explicit
  per-row `reopen_condition` or regression mode.
- `equilibrium` — halts at frontier-exhausted balance; reopens **automatically**
  on strong new signal (externally reviewed finding, typed failing trace, oracle
  verdict, metric movement).
- `checkpoint-with-reopen` — halts on convergence but explicitly reopens when a
  new authoritative source / route / fixture appears (the story shape; bodytxt
  keeps it, sourced from story).
- `manual-gated` — the loop persists by design and only the human ends it, via a
  `Next action: HALT` hatch (owner: user).

## Detection heuristics

| Task phrasing | Value |
|---|---|
| finite spec, "done when criteria pass" | `terminal` |
| "keep improving until balanced", "stop when nothing's disturbed" | `equilibrium` |
| "living contract, revisit when sources change" | `checkpoint-with-reopen` |
| "run until I say stop", "I'll keep reframing" | `manual-gated` |

## Archetype defaults

| Archetype | Default |
|---|---|
| frontier | `equilibrium` |
| goal | `terminal` |
| story | `checkpoint-with-reopen` |
| greenfield | `manual-gated` |

## Composition rules

- `manual-gated` is **forbidden** for `goal` — a terminal loop only the human
  ends is not terminal.
- Orthogonal to `convergence-shape`: one names the reopen policy, the other the
  stop signal. Provenance lists both when either diverges.
- `checkpoint-with-reopen` is `story`'s default, which is why bodytxt (a story
  loop with a `frontier-expanding` target) sources it from the story archetype
  (not as a brand-new value).
- **Contradiction** (ask the user): `equilibrium`/`manual-gated` with
  `target-shape: finite-criteria`.
