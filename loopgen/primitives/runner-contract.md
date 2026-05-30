# Runner Contract (shared primitive)

**Purpose.** Declares that the emitted prompt is runner-agnostic — `/loop`,
`/goal`, and external harnesses (gnhf, cocc, ralph) all re-invoke it; they
differ only in invocation. Establishes the four things the prompt assumes
about its host and the boundary between a runner ceiling and a repository
failure.

**Include when.** Every composed prompt, every archetype, unconditionally.
This block is universal.

**Placeholders.** None — substituted verbatim.

**Source of truth.** Byte-identical to the `## Runner contract` block in the
retired `frontier-loop` / `goal-loop` / `story-loop` prompt templates
(verified at hoist time). After retirement, this file is canonical.

**Idempotency corollary (authoring guidance — not part of the inlined block).**
Because the runner re-invokes the *same* prompt every iteration, the emitted
prompt must be safe to re-run from any state: all first-iteration / bootstrap /
one-time setup must be **self-gated on durable state** (`loop/STATE.md`
`iteration: 0`, "no `<artifact>` yet"), run once, then skipped — never assumed to
run "first". The **kick-off** invocation the operator pastes into the runner must
likewise be iteration-agnostic (a pointer to the prompt, no "begin with…" step) —
it is re-sent unchanged each iteration. Canonical self-gate shape: story-body's
`## Bootstrap mode`. See SKILL.md Phase 4 → "The kick-off (runner invocation)".
Dogfooding citation: mdtools hybrid-pareto goal loop (2026-05-29) — a kick-off
that said "begin with its First-iteration bootstrap" would have re-run inventory
instantiation on iteration 2; fix was to gate the bootstrap on `iteration: 0`
inside `PROMPT.md` and reduce the kick-off to a stable pointer.

---

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
