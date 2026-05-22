# Frontier-model review — 2026-05-22

Five ChatGPT Pro (extended) reviews of the loop skills: one adversarial design
review per skill, plus one cohesive cross-family review. This file triages the
findings — what was applied directly, and what needs a design decision.

## Cross-cutting patterns

1. **Advisory → enforceable.** Every skill names the right concept but leaves
   it to the loop's judgment. A long-lived autonomous loop exploits that. The
   recurring fix: hard gates with required fields / machine-checkable closure.
2. **Stale-target re-grounding.** Loops self-certify with self-authored
   artifacts (ledger prose, JSON they wrote). Strong signal must be external
   or machine-derived; self-authored memory cannot reset a starvation detector
   or stand in for product intent.
3. **Boundary integrity.** Each loop can quietly become the wrong loop type
   (Spark drifting into frontier search; frontier closure-mode competing with
   Spark). Loops need explicit "wrong loop — reroute" tripwires.
4. **Two real contradictions** (now fixed) — see Applied.

## Applied (this commit)

Surgical, unambiguous fixes — bugs, public hygiene, and the clearest loophole
patches. No skill changed identity or structure.

| Skill | Change |
|---|---|
| frontier-loop | **Bug:** ramp trigger said stages 3–6, but ramp-exit needs only 1–5 — a loop could be told "still ramping" and "must exit" at once. Trigger is now stages 1–5; stage 6 absence is post-ramp evaluator debt. Added a mandatory `ramp-complete` exit. |
| frontier-loop | Quiet-signal counter now resets only on *strong* signal (external finding, typed trace, oracle verdict, metric move) — not on self-authored ledger prose. Closes the "narrate new work to stay alive" loophole. |
| greenfield-loop | **Bug:** emit step 5 told the loop to render iteration-0 output before the rubric existed, contradicting invariant 1 (evaluator scaffolding precedes artifact). Step 5 is now evaluator-first. |
| greenfield-loop | Doc drift fixed: "10 invariants" → 11; "Three files" → four (README was already emitted). |
| greenfield-loop | **Public hygiene:** removed a private absolute path from Provenance. |
| spark-loop | Closure-fit classification tightened: requires a *discriminative* proof channel and an *admissible concrete anchor* (not a generic seam class); bootstrap budget is one move for the whole run. |
| story-loop | **Public hygiene:** "xBanker-specific" → "project-specific". |
| story-loop | New Forbidden Move: the storyboard is not product intent — re-check an authority source before verifying/promoting an existing story. |

## Deferred — needs your decision

These are real and well-argued, but they change structure or identity. Not
applied autonomously.

### Family structure (from the cohesive review)

- **Missing `goal-loop` / oracle-loop.** The repo's identity is "generators
  for `/goal` and `/loop`", and frontier-loop explicitly contrasts itself
  with an "Oracle loop" — but no skill owns finite, known-target, terminal
  work (implement a known spec, pass a fixed suite, close a finite
  checklist). This is the biggest gap. Either author `goal-loop`, or have the
  router declare finite oracle work out-of-family.
- **story-loop is not shaped like the others.** frontier/greenfield/spark are
  prompt-generators; story-loop reads as an operational workflow. Either
  normalize it into a generator (emit `loop/PROMPT.md` etc.) or split it out.
  *(You already flagged this as "later".)*
- **`references/loop-family-router.md`** — one authoritative routing decision
  tree; each skill defers to it instead of re-deriving "when to use".
- **`references/shared-loop-contract.md`** — factor shared vocabulary
  (memory-surface hierarchy, reward/proof channels, closure statuses,
  quiet-signal) into one cited file so the four skills stop drifting.
- Explicit deflection rules: frontier↔greenfield (don't let frontier absorb
  empty/vague repos), frontier↔spark (closure mode vs spark-loop).

### Larger per-skill enhancements (drop-in text exists in the reviews)

- **frontier-loop:** stage-closure "evidence cards"; a repo-specific
  "frontier vector"; anchor lifecycle (stale-anchor depletion); evaluator
  cash-out discipline.
- **greenfield-loop:** score-locked ramp state machine; role-protected phase
  gates (loop/user/external ownership); `INTENT.md` competing target
  hypotheses; blind audience-comprehension tests; rubric versioning + score
  quarantine; blind adversarial consult protocol.
- **spark-loop:** in-prompt frontier tripwires; a "wrong-loop checkpoint";
  carry `FIXED ≠ CLOSED` from frontier-loop; constrain the
  `claim-falsification` / `probe-and-preserve` seam families.
- **story-loop:** explicit Bootstrap Mode; split `ready` into intent-ready
  vs proof-ready; a non-QA "no ready story" fallback; a frontier-novelty
  rule (don't re-verify easy stories); negative-space promise scan.

The full review text for each skill is preserved in the ChatGPT Pro threads.
