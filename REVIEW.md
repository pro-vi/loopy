# Review log

Compressed history of design passes on the loopy family, plus the
candidate list awaiting dogfooding evidence per the
**[promotion discipline](README.md#promotion-discipline)** in the README.

This file is a log, not a place where new rules earn their seat. Live
discipline lives in the README and in each skill's emitted template.

## Changelog

| Pass | Date | Summary | Key commits |
|---|---|---|---|
| 1 | 2026-05-21 | Five frontier-model reviews (one per skill + cohesive). 8 safe fixes applied: two real bugs (frontier ramp contradiction; greenfield emit-step rendering before evaluator) + public hygiene (private paths, xBanker leak, 10/11 invariants) + loophole patches (frontier quiet-signal provenance; spark closure-fit anchor admissibility; story storyboard-not-authority). | `a20f1dc` |
| 2 | 2026-05-22 | story-loop restructured from operational workflow → prompt generator (matching frontier / spark / greenfield shape). Family aligned: runner-agnostic + judgment-default. Two shared blocks (Runner contract · Judgment default) duplicated per skill to survive the symlink install model. | `eef81fe` · `8c4e650` |
| 3 | 2026-05-22 | 19 hand-tunes triaged interactively and applied (frontier ×4, greenfield ×7, spark ×4, story ×5; cash-out softened to `{{CASH_OUT_N}}=3`; story slimmed from nested YAML into terse contracts; greenfield gained consult-availability detection; spark intro tightened with plain language). | `0f7bddf` · `9d29c7e` · `17689b9` · `7e4d3a2` · `466169c` · `596b49e` · `a627ca8` |
| 4 | 2026-05-22 | Frontloading discipline + halt-cause classifier as family-level principle. README opens with "loops that don't block." Pre-flight Frontload audit step added per skill; halt-cause `derivation-gap` labels become the feedback signal that retrofills the next derivation's checklist. | `06bffd2` · `5b9367c` · `144fd33` · `ac2de89` |
| 5 | 2026-05-22 | `goal-loop` landed as the family-voice distillation of a heavyweight oracle-loop framework. Kept: 4 oracle principles + ~7 honest-verifier invariants + PASS_PENDING_FINAL→PASS state distinction + Oracle Change Notes (inline) + anti-patterns. Dropped: multi-agent prompt split · `loop.sh` · Phase + TICKET-ID decomposition · `[lane:acceptance]` tags · Motivation Trace + Risk-to-Oracle as separate steps · Representation Boundary Analysis. | `90f7cfc` |
| 6 | 2026-05-22 | Public-repo hygiene: scrubbed external personal-skill references across all siblings (replaced with generic capability descriptions); `/architect` kept as a *named example pattern*. | `68c6ed8` |
| 7 | 2026-05-23 | `spark-loop` retired. Second-opinion consultation (ChatGPT Pro Extended, PAL trio unavailable) returned "demote, don't delete" at confidence 0.74; owner went further: remove entirely. Cross-references updated (sibling halt-cause `wrong-loop` reroutes drop spark as target). | `accfbc3` · `2793c2c` |
| 8 | 2026-05-23 → 2026-05-24 | `god-loop` drafted as experimental meta-skill (homeostasis over modes), then retracted within 24h without dogfooding. A second-opinion consultation on family coverage delivered the same verdict as the prior /meta: *"add only when real runs need it; pick empirically-earned shapes over full coverage."* The bar that retired spark applied to god-loop. Content preserved at `609ba2a` for restoration if real multi-mode usage later earns it. | `609ba2a` · `53d3378` |
| 9 | 2026-05-24 | **Promotion-bar rule moved to README** as family-level discipline (was buried in REVIEW.md's Still-deferred section). **Skill Harvest** pattern propagated from story-loop (where another agent's dogfooding-derived edits introduced it) to frontier-loop · goal-loop · greenfield-loop, so all siblings emit the dogfooding-citation capture mechanism the promotion-bar requires. REVIEW.md slimmed from 8-pass narrative to this changelog. | (this commit) |

## Logged candidates (awaiting dogfooding evidence)

Each entry below names the *run signal* that would earn promotion to
`main`. Until that signal is observed in a real Skill Harvest note, the
content stays in the candidate list.

| Candidate | Source | Earns promotion when… |
|---|---|---|
| `inquiry-loop` | GPT Pro coverage-gap consultation, 2026-05-23, confidence 0.78 | Real debug / RCA arcs keep getting mis-shaped as fixes by `goal-loop` or `frontier-loop`; the artifact-of-interest is a *finding* (causal explanation / narrowed surviving hypotheses), not a fix. |
| `audit-loop` / cartography | GPT Pro coverage-gap consultation | `inquiry-loop` doesn't subsume the work and `goal-loop` preflight inventories aren't strong enough for population-mapping tasks (security surfaces · dep audits · API route inventories). |
| `god-loop` (retracted) | Owner intuition + retracted draft at `609ba2a` | A real run genuinely shifts between ≥2 modes within one session and manual switching is observably worse than a runtime classifier. |
| Authority-resolution overlay | GPT Pro coverage-gap consultation | A sibling repeatedly emits `source-conflict → escalate` halt-cause across real runs. |
| Migration-as-goal-loop overlay | GPT Pro coverage-gap consultation | Real migration runs through `goal-loop` repeatedly lose path-safety invariants (pre-state captured · shim · dual-read · rehearsal · rollback · removal · final-verify). |

## Permanently deferred (structural)

- **Shared `references/loop-family-router.md` + `shared-loop-contract.md`.**
  A truly shared reference dangles under per-skill symlink install. The
  current resolution is per-skill duplication of the short Runner-contract
  / Judgment-default / Frontloading / Skill-Harvest blocks. A router file
  would need either heavy per-skill duplication or an installer that
  brings the family together — neither has earned the cost yet.

## Lineage notes

- `goal-loop` is the family-voice distillation of a heavyweight oracle-loop
  framework owned by the author (multi-agent Builder/Acceptance, `loop.sh`,
  Phase + TICKET-ID decomposition, structured `PROGRESS.md` /
  `PROMPT_build.md` / `PROMPT_acceptance.md` emission, full 18-invariant
  admissibility checklist). Reach for the heavyweight form when stakes
  require strict role separation or a generated `loop.sh` runner.
- `story-loop`'s same-family drift guard + story-movement gate + Skill
  Harvest note all entered the repo via another agent's dogfooding-derived
  edit, then propagated through the family in pass 9 — the first
  family-internal application of the promotion-bar rule.
