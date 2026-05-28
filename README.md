# loopgen

Prompt generators for `/goal` and `/loop`.

`loopgen` is a small family of Claude Code skills that **derive repo-specific
prompts for autonomous improvement loops**. The skills do not run the loop —
they author the prompt (and the invariants) that a **runner** then executes.
`/loop`, `/goal`, and external harnesses (gnhf, cocc, ralph) are all valid
runners; the same emitted prompt runs under any of them.

## Design principle: loops that don't block

The biggest failure mode of an autonomous loop is *self-terminating at 3 AM
blocked on a decision the user could have made before the loop fired*. All
four skills share one principle to prevent that:

- **Frontload at derivation.** For every uncertainty the loop might need
  (model, command, path, credential, evaluator, scope, fixture, …),
  *resolve it now* (AskUserQuestion if the host has the tool, otherwise
  print prominently), *default + Alignment Review*, or *mark as escalate
  candidate*. Anything left over is a **derivation gap** — a future block.
- **Judge at iteration.** When the loop *does* hit a judgment-bearing call,
  it picks the smallest reversible action consistent with the strongest
  available source, records an Alignment Review (problem · options ·
  chosen contract · rollback trigger · review question), and continues.
  Human review happens after the fact.
- **Escalate only the irreversible.** `escalate: <reason>` is reserved for
  paid APIs without budget caps, public-publish actions, secrets, and
  product direction with unclear rollback. Everything else is judgment.

Each emitted prompt also carries a **halt-cause classifier** so the user
can route a halt back: `derivation-gap` means "you should have asked me
this", `genuine-escalate` means "I correctly punted", `frontier-exhausted`
/ `stone-converged` / `seams-sealed` / `storyboard-converged` /
`criteria-met` mean legitimate completion.

## Promotion discipline

A new sibling skill, overlay, or structural refactor lands on `main`
only with a **dogfooding citation** — a concrete run on a real project
that surfaced the failure mode the addition addresses. Frontier-model
recommendations and design intuitions log as candidates (below); drafts
wait for a real run before promoting.

The capture mechanism is the **Skill Harvest** pattern that each sibling
template emits as part of its iteration / reconcile protocol: when a run
reveals a generalizable lesson, the loop writes a structured harvest
note (target skill · observed gap · evidence iteration · proposed rule ·
why it generalizes · suggested patch wording · accidental-encouragement
risk). Promotion to `main` requires at least one such citation.

This bar already produced `spark-loop`'s retirement (taxonomy hadn't
earned a public slot) and `god-loop`'s retraction (drafted then pulled
when it failed the same bar). Restoration of either, if real usage ever
earns it back, is a `git revert` away.

The bar is empirical, not aesthetic. *"Full coverage"* is rejected as a
design goal — the family grows from observed failure modes, not from
theoretical gaps.

### Candidates (awaiting dogfooding citation)

| Candidate | Earns promotion when… |
|---|---|
| `inquiry-loop` | Real debug / RCA arcs keep getting mis-shaped as fixes by `goal-loop` or `frontier-loop`; the artifact of interest is a *finding* (causal explanation / narrowed surviving hypotheses), not a fix. |
| `audit-loop` / cartography | `inquiry-loop` doesn't subsume the work and `goal-loop` preflight inventories aren't strong enough for population-mapping tasks (security surfaces · dep audits · API route inventories). |
| `god-loop` (retracted) | A real run genuinely shifts between ≥2 modes within one session and manual switching is observably worse than a runtime classifier. |
| Authority-resolution overlay | A sibling repeatedly emits `source-conflict → escalate` halt-cause across real runs. |
| Migration-as-goal-loop overlay | Real migration runs through `goal-loop` repeatedly lose path-safety invariants. |

Sources: GPT Pro coverage-gap consultation 2026-05-23 + owner design
intuition. Each candidate's earn-promotion signal is the failure mode a
harvest note would have to cite.

## The skills

| Skill | Derives a loop that… | Use when |
|---|---|---|
| `goal-loop` | the **terminal loop** — closes a finite acceptance inventory and halts when one final-verify proves the whole list (a 1-row inventory is fine for closing a single bug or review finding) | you have a known list of things to make true (spec, suite to pass, `/architect`-style blueprint, one bug with a repro) |
| `frontier-loop` | moves a repo's evidence-backed capability frontier | an artifact/evaluator exists and should get better |
| `greenfield-loop` | discovers what to build when nothing is defined | empty repo, vague "build me something X-adjacent" intent |
| `story-loop` | maintains a living product storyboard | discovering / verifying user-facing product promises |

Rough routing: **finite known target →** `goal-loop` · **target undefined →**
`greenfield-loop` · **move an existing frontier →** `frontier-loop` ·
**product promises →** `story-loop`.

## Install

Clone, then symlink the skills into your agent's skills directory:

```sh
git clone git@github.com:pro-vi/loopgen.git
for s in frontier-loop goal-loop greenfield-loop story-loop; do
  ln -s "$PWD/loopgen/$s" ~/.claude/skills/$s      # Claude Code
  ln -s "$PWD/loopgen/$s" ~/.codex/skills/$s       # Codex (optional)
done
```

Each skill is a plain directory with a `SKILL.md` — no plugin runtime, so it
works with any agent that reads skill folders.

## Notes

`goal-loop` is the family-voice distillation of a heavyweight oracle-loop
framework — same wisdom (binary oracle, oracle independence, consumer-
side, anti-theater), minus the syntax-heavy bureaucracy (multi-agent
prompts, `loop.sh`, tagged lanes, Phase + TICKET-ID decomposition). The
runner contract handles iteration; the LLM handles execution. See
[`goal-loop/references/oracle-principles.md`](goal-loop/references/oracle-principles.md)
for the principles. Design history lives in `git log` — no separate
log file.
