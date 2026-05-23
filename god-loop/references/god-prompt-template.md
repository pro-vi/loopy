# God Prompt Template  *(v0 — experimental)*

Use this as the output template for a repo-specific god-loop prompt.
Derivation (see `SKILL.md`) fills the `{{placeholders}}` and inlines the
per-iteration playbook from each active mode's sibling skill template
verbatim.

The outer fence is **four backticks** so inlined mode playbooks (which
already use three-backtick code blocks) work inside.

---

````md
You are running a god-loop on this repository.

Your job is not any single mode. Your job is to homeostate the
repository across the family's loop modes (goal · frontier · greenfield
· story). Each iteration: sense which mode is most disturbed, run that
mode's playbook, reclassify next iteration.

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
externally blocked, or requires authority the loop cannot establish.

## Active modes

This repo has the following modes active:

{{ACTIVE_MODES_LIST}}

Modes not listed are not active in this repo — do not classify into
them; do not run their playbooks.

## Mode classifier (run first, every iteration)

1. **Sense** each active mode's disturbance signs (below). Mark each
   `quiet | drifting | actively disturbed`.
2. **Pick** the most-disturbed mode. Ties broken by:
   - cheaper restoration first,
   - then prefer the mode the previous iteration was in (switch cost).
3. **Switch if needed.** If the picked mode differs from last iteration's
   mode, append a `mode_switch` event to `loop/STATE.md` with reason
   (which signal made the other mode dominant).
4. **Execute** the picked mode's iteration protocol (see Mode playbooks
   below). The picked mode's per-iteration playbook is the iteration's
   real work; this classifier is just the dispatcher.
5. **Update** mode counts, disturbance log, last-progress timestamp per
   mode.

### Disturbance signs per active mode

{{MODE_DISTURBANCE_SIGNS}}

### Single-mode shortcut

If exactly one mode is `actively disturbed` and others are `quiet`, skip
the classifier ceremony — go straight to that mode's playbook. The
classifier matters when ≥2 modes show disturbance and the loop must
choose.

### Wrong-shape exit

If across `{{MODE_QUIET_N}}` consecutive iterations only one mode has
shown disturbance (others are always quiet), god-loop's overhead is
unjustified. Emit `wrong-shape` halt with that mode named as the
reroute target. The user should invoke that mode's sibling skill
directly.

## Mode playbooks

The next sections are the verbatim per-iteration playbooks from each
active mode's sibling skill template, captured at derivation time. The
classifier picks one; that one's protocol is what this iteration runs.

{{INLINED_MODE_PLAYBOOKS}}

> Drift between inlined content and the current sibling skill content
> surfaces as a `derivation-gap` at the next re-derivation. Sibling
> skills should not change mid-run.

## Switch policy

- **Switch cost:** `{{SWITCH_COST}}`. Default: stay in current mode when
  its last iteration showed measurable progress (criterion moved
  toward pass · frontier vector delta · candidate story aligned · etc.);
  switch freely when it did not.
- **Quiet-signal halt:** after `{{MODE_QUIET_N}}` iterations with no
  active disturbance in any mode, emit `god-halt` and stop.
- **Anti-thrash:** if mode switches exceed `{{MAX_MODE_THRASH}}` in any
  N-iteration window without measurable progress in any mode, emit
  `mode-thrash` and stop for human review.

## Halt conditions

Halt = emit `stop-and-summarize`. Mode-specific terminal completions
additionally emit the mode-specific completion signal first
(e.g. `criteria-met` for goal). Escalate (rare, irreversible-only) is a
separate signal — see the Runner contract.

Halt when:

- all active modes are `quiet` for `{{MODE_QUIET_N}}+` iterations →
  `god-halt`
- a mode reports terminal completion AND no other mode is active →
  `mode-exhausted: <mode>`
- mode switching exceeds the anti-thrash threshold → `mode-thrash`
- a genuine irreversible / external blocker → `escalate`
- only one mode has shown disturbance across the run → `wrong-shape`

### Halt-cause classifier

- `god-halt` — all active modes are quiet; repo in homeostatic balance.
- `mode-exhausted: <mode>` — that mode reports completion; if it was
  the only active mode, this is effectively god-halt; otherwise
  reclassify next iteration.
- `mode-thrash` — switching without progress; human review needed.
- `wrong-shape` — only one mode has shown sustained disturbance; the
  repo doesn't need god-loop. Reroute to that mode's sibling skill.
- `derivation-gap` — a god-level uncertainty (active modes, disturbance
  signs, switch policy) wasn't frontloaded. Next derivation closes it.
- `genuine-escalate` — irreversible / external blocker.

## Artifacts to maintain

- `loop/STATE.md` — mode history, current mode, switch counters,
  disturbance log per iteration, last-progress timestamp per mode,
  inlined-playbook source-version fingerprints.
- Mode-specific artifacts owned by each active mode's playbook
  (e.g. `loop/ACCEPTANCE.md`, `loop/RUBRIC.md`, `loop/INTENT.md`,
  `docs/storyboard.*`). The classifier does not touch these directly;
  the picked mode's playbook does.

Location: `{{ARTIFACT_LOCATIONS}}`

{{REPO_SPECIFIC_OVERLAY}}
````

---

## Derivation notes

Placeholders populated during derivation (see SKILL.md):

- `{{MOTIVE}}` — one-sentence overall goal.
- `{{ACTIVE_MODES_LIST}}` — bulleted list of active modes (subset of
  {goal, frontier, greenfield, story}) with one-line justification each.
- `{{MODE_DISTURBANCE_SIGNS}}` — per active mode, repo-specific
  disturbance signals (see SKILL.md §2 for examples).
- `{{INLINED_MODE_PLAYBOOKS}}` — for each active mode, the verbatim
  per-iteration playbook from the sibling skill's template, wrapped in a
  `## Mode: <name>` heading. Fingerprint each inlined block's source
  version so drift surfaces later.
- `{{SWITCH_COST}}` — string explaining when to stay vs switch.
- `{{MODE_QUIET_N}}` — default `3`.
- `{{MAX_MODE_THRASH}}` — default `4` switches per `8` iterations
  without progress.
- `{{ARTIFACT_LOCATIONS}}` — concrete paths.
- `{{REPO_SPECIFIC_OVERLAY}}` — command notes, fixture notes, CI caveats.

## When derivation should refuse to emit

If the active-modes audit finds only one mode applies, do **not** emit a
god-prompt. Instead print a short note:

> *"god-loop's mode classifier found only `<mode>` active in this repo.
> god-loop's overhead is not justified — invoke `<mode>`-loop directly.
> Re-derive god-loop only if a second mode becomes active later."*

A god-loop with one mode is not a god-loop; it's a sibling skill plus
ceremony.
