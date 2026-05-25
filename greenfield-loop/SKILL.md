---
name: greenfield-loop
description: "Derive a repo-specific prompt for a runner-agnostic autonomous loop in green-field territory - artifact, metric, and target all undefined at start. Distinct from /frontier-loop. Also retrofits a drifted loop."
---

# Greenfield Loop

This skill emits a repo-specific **runner-agnostic** prompt — `/loop`,
`/goal`, or an external harness can all run it — when the loop begins in
**green-field territory**: empty repo, vague creative intent, no pre-existing
benchmark, no fixed evaluator.

**Distinct from `/frontier-loop`.** Both produce loop prompts that share many
hard rules (vision-mandatory scoring, closure discipline, anti-idle, persistent
by design). The difference is the *starting state*:

| Axis | `/frontier-loop` | `/greenfield-loop` |
|---|---|---|
| Artifact | Exists; loop improves it | Doesn't exist; loop discovers what to build |
| Evaluator | Pre-defined (CI green, benchmark, user metric, oracle) | Manufactured by the loop; revised as a real product step |
| Target | Optimize one fixed thing | Phase-shifts as intent reveals itself |
| Convergence | Asymptotic on a single number | Multiple reframes; ceilings demolished |
| User role | Approve, redirect, accept | Curate intent, name failure modes, raise ceiling, reframe target |
| Capability set | Fixed | Expanding (loop installs new tools when they advance the stone) |
| Risk | Goodhart's law on the metric | Self-congratulation on a wrong target |

If the project has *any* of: an existing benchmark, a pre-built thing to make
better, an oracle the loop optimizes against, a CI signal, a known-correct
output — use `/frontier-loop`. If the project is *"build me something
roughly-shaped-like-X but I'll know it when I see it"* — use this skill.

**Invoke once per run** to author or revise the prompt. Output: the body of
`loop/PROMPT.md` (or amendments to an existing one) + initial `loop/RUBRIC.md`
+ initial `loop/STATE.md`. The runner re-invokes the prompt; this skill
writes it.

## When to invoke

Trigger on user phrases like:
- "build me something X-adjacent" (e.g. Kurzgesagt-adjacent, Notion-adjacent,
  Figma-adjacent — *adjacency* without specification is the green-field tell)
- "I want an agent that iterates toward Y" with Y under-specified
- "let's start a /loop on this empty repo"
- "make a content engine / design engine / writing engine that…"
- "I want to discover what the right shape of this product is"

Also invoke to **retrofit a drifting existing loop** that shows any of:
- Frequent pause-polling on user-gated questions (escalating where judgment should default)
- Score milestones hit faster than expected, repeatedly
- Self-scoring bare/cosmetic outputs at high numbers
- 100+ untracked artifacts (no commit discipline)
- The user keeps reframing the target across sessions
- Loop hasn't tried anything *not* obviously copying a reference recently

## Do NOT invoke when

- Project has an explicit benchmark, oracle, CI signal, or pre-built evaluator → use `/frontier-loop`
- Known finite acceptance criteria (spec, suite to pass, checklist) → use `/goal-loop`
- One-off planning (not iterative) → use a dedicated planning skill, not a loop
- Pure debugging → use a dedicated debugging tool, not a loop

## What this skill produces

Four files, all in `loop/` at the repo root:

### 1. `loop/PROMPT.md`

The persistent prompt the user pastes into the runner (`/loop`, `/goal`, or
an external harness). Carries the green-field invariants below. **The
prompt is the load-bearing artifact** — every commit in this skill's lineage
was driven by a failure mode that surfaced because the prompt missed an
invariant.

### 2. `loop/RUBRIC.md`

The initial measurement framework. Numbered criteria (typically 8–12 to
start), 0–5 scale, with concrete pixel/artifact-level anchors. Built to be
**revised as the artifact reveals itself** — the rubric is not a fixed truth.
Hard rule: every score above 2 requires citation evidence.

### 3. `loop/STATE.md`

Phase, iteration counter, current open seams, last action, and a `Next
action: HALT` exit hatch the user controls. Replaces itself as the loop
diagnoses imbalance each firing.

### 4. `loop/README.md`

How to fire the loop, how to tune the rubric, how to halt, and what
milestones look like.

## Frontloading discipline

Loops fail when they self-terminate blocked on a decision the user could
have made before the loop fired. The derivation step's job is to
**frontload every uncertainty** so iteration runs through the night
without blocking.

At derivation time, for every thing the loop might need (model, paid-API
budget, secret, license click, hardware, evaluator format, capability
surface, consult channel, …), do one of:

1. **Resolve it now.** If the host has an AskUserQuestion-style tool,
   use it. Otherwise print the uncertainty prominently in the derivation
   response so the user can answer before launching.
2. **Default + Alignment Review.** Pick the smallest reversible default
   and record an Alignment Review.
3. **Mark as escalate candidate.** Only for the truly irreversible.

Anything left over is a **derivation gap** — a future 3 AM block waiting
to happen. The Pre-flight Frontload audit (in the emit procedure) lists
what was resolved / defaulted / deferred; the emitted PROMPT.md's
halt-cause classifier flags `derivation-gap` halts so the next
derivation pass closes them.

## The 11 green-field invariants (encoded in the emitted prompt)

These are the patterns that *every* successful green-field loop converges on
empirically. Encoding them up-front saves 50–100 iterations of rediscovery.

### 1. Evaluator scaffolding precedes artifact

The first 3–7 iterations are *rubric construction*, not output. The loop
authors `loop/RUBRIC.md`, picks initial criteria, writes concrete pixel/
artifact-level anchors, and only then renders the first artifact. If the user
demands output before evaluator, the prompt should warn that early outputs
will inflate.

**Score-locked ramp.** STATE.md starts with `score_lock: yes`. While
locked the loop may not write numeric totals, pass/fail judgments,
milestone claims, or artifact-to-artifact rankings — only rubric drafting,
evidence-rule definitions, the cheap dignity test, audience-comprehension
probes, and explicitly labeled `CALIBRATION — NOT PRODUCT — NOT SCORED`
runs. Exit the lock only when RUBRIC.md v0.1 has 8–12 criteria with
0/2/5 anchors and per-criterion evidence rules, the cheap dignity test
is written, an audience-comprehension probe is defined, and `INTENT.md`
holds live target hypotheses (see #3).

### 2. Pixel/artifact-level evidence is mandatory for any score above 2

The single most reliable failure mode in green-field is *agent-grades-own-
homework*. The fix is structural: every score above 2 requires citation
evidence (extracted frames, AST nodes, test outputs, transcript spans —
whatever the artifact is). No citation = score caps at 2. Run a "would this
pass the cheap dignity test" guard (Keynote-slide-guard equivalent) before
writing the total.

**Blind comprehension for audience-facing scores above 3.** For criteria
involving clarity, narrative, comprehension, audience fit, persuasion, or
memorability: scores above 3 require a blind read — send the artifact to
a fresh isolated session (LLM via the invariant-8 CONSULT channel, or a
human reviewer) **without** the rubric, prompt, internal rationale, or
current scores. Ask: *"what is this trying to do? what did you
understand? what felt missing, confusing, derivative, or hollow?"*
Compare to the artifact's `comprehensionClaim`. Cap the criterion at 2
if no blind read was run; cap at 3 if the blind read substantially
diverges from the claim.

### 3. The intent itself will change. Build for that.

In green-field, the user's "I want X-adjacent" is provisional. They will
look at output and reframe — typically 2–4 times before the actual target
locks in. Encode:
- Schedule explicit **user-look gates** every ~50 iterations (not frequent
  pause-polling — substantive review milestones)
- Treat any user reframe as a first-class iteration that may invalidate
  prior scores; write a `STATE.md` block that explicitly resets stale
  numbers when intent shifts
- Don't let the loop defend old work against new intent
- Maintain `loop/INTENT.md` with **≥3 live target hypotheses**: the
  literal interpretation, a more ambitious / more original interpretation,
  and a dangerous-but-plausible wrong interpretation. Each names
  supporting evidence, artifact implications, what good output makes the
  user understand/feel/do, failure smells, invalidating evidence, and one
  cheap probe that distinguishes it. No rubric criterion may be added
  unless it maps to a live hypothesis. Re-review INTENT.md every ~10
  iterations, on every user reframe, and before any major rubric revision.

### 4. Hard-coded numerical targets are scaffolding, not goals

Numerical milestones (45/50, 3 essays × 3 styles, etc.) prevent drift
early. They WILL become ceilings once hit. Encode:
- The rubric is replaceable; the philosopher's stone is not
- When milestone is hit "faster than expected," that's evidence the
  rubric was too easy — *raise the ceiling, don't celebrate*
- Once the user reframes to "no fixed milestone," switch to imbalance-
  seeking (see #5)

**Rubric versions + score quarantine.** `RUBRIC.md` carries
`rubric_version: vN` and `score_comparable_with: [...]`. Any of these
creates a new version and quarantines old scores: user reframe,
target-hypothesis change, major artifact-format change, milestone hit
faster than expected, three consecutive high scores with no substantial
audience/capability change, a frontier-consult flagging stale-target
risk, or a blind-comprehension read that diverges from self-scores.
During quarantine the loop may not claim improvement against the old
total; it must revise the evaluator, run a divergence probe, or
rebaseline under the new version. "Raise the ceiling" must be a new
version, not a stricter restatement of stale criteria.

### 5. Imbalance-seeking replaces sequenced plans eventually

Sequenced plans (N+1, N+2, …) are useful early — they give the loop
something to climb. Eventually the user will say "stop following a script."
At that point switch to imbalance-seeking: every iteration begins with
diagnosis of the *currently most under-developed* dimension of the stone.
Diagnostic categories: output quality (rubric criteria), capability surface,
style/voice emergence, agent experience, creative direction.

### 6. CAPABILITY mode is first-class

Green-field loops *grow capability* — install new tools, integrate new
substrates, wire new modalities — when those advance the stone. This is not
"yak-shaving"; it's the loop's job. Encode CAPABILITY as priority 0 or 1
in the mode menu, with the discipline rule: **must advance the stone, not
pad the toolbelt.** Each addition justified against a stone-axis.

### 7. Judgment default + bounded escalate

Pausing for human input is the polling-shaped failure mode. The default is
**narrow reversible judgment + Alignment Review**: pick the smallest
reversible action consistent with the strongest available source, record
problem · options · chosen contract · alignment cost · rollback trigger ·
review question, and continue. Topic, content, style, scene-direction,
essay-text are all the loop's job — make the call, log it, move on. Human
review happens after the fact.

Emit `escalate: <reason>` only for genuinely irreversible / external
blockers: **paid APIs without budget caps, public-publish actions, and
secrets/credentials** — nothing else.

Saturation rule: 2 consecutive escalates on the same logical question force
a default judgment on the third.

### 8. Frontier-model consults are creative direction, not architecture review

Schedule a CONSULT every ~10 iterations. Frame the question as:
*"what's seductive-but-hollow about recent progress?"* and *"what omission
would look stupid in 6 months?"* — not "review my plan." Disagreement with
the consultant is allowed and sometimes correct. Capture into
`loop/creative-consults.md`.

**Blind adversarial protocol.** The consult packet excludes current
scores, the loop's preferred next plan, and self-justifying rationale.
It contains: the user's original intent + later reframes, current
INTENT.md hypotheses, representative artifacts or excerpts, the current
rubric criteria *without scores*, and known constraints. Ask:

1. What current target should be killed or demoted?
2. What's seductive-but-hollow about recent progress?
3. What would look derivative, trivial, or embarrassing in six months?
4. What assumption is the loop over-optimizing?
5. What one experiment would most cheaply invalidate the current rubric?

Within 3 iterations the loop must do one of: run a proposed invalidation
experiment, revise INTENT.md / RUBRIC.md because of the answer, or
explicitly reject the answer with a concrete reason. A consult that
produces no behavioral change and no explicit rejection does **not** count
as completed.

**Consult availability detection (at derivation time).** Inspect the
host environment for a frontier-model consult channel (Agentify Desktop
MCP, PAL, mcp__* tools, or similar). If none is available, mark
invariant 8 in the emitted PROMPT.md as
`CONSULT unavailable in this environment — front-loaded as a known
limitation; the loop proceeds without scheduled creative consults and
surfaces creative-direction blindness as a known risk.` Do not silently
drop the invariant — the user should see at run-start that CONSULT is
off, and consider a periodic human-look gate instead.

### 9. Provenance/re-editability matters from the first generated asset

The moment the loop produces an asset that wasn't deterministically
authored from code (image-gen, video-gen, vendored SVG, generated text),
provenance becomes load-bearing. Encode a slot-manifest contract with at
minimum: `slotId, semanticRole, source, prompt, seed, replaceability,
comprehensionClaim`. The `comprehensionClaim` field — what should this
asset make the viewer/reader/user understand? — is the linchpin of
honest scoring later.

### 10. Human-only work front-loaded into a gated preloop phase

Greenfield projects with hardware, secrets, license clicks, network setup, or
identity decisions (which model? which account? which physical machine?) must
*not* fold those into iter 0. They belong in a **preloop phase** that gates
on a binary completion flag (e.g. `preloop_complete: yes`) the human flips
when the human-only checklist is done. Without this gate, the loop will:

- Spin on escalates for things only the user can do, polling-shape instead of moving
- Wrong-default decisions that should be the user's (which model? which tunnel?)
- Mix human-decision velocity with autonomous-iteration velocity, distorting
  scoring and pacing

When emitting the prompt, identify human-only items in the project's domain
and write them as a numbered preloop checklist with matching fields in
STATE.md. Add a **gate-hardening line**: the gate is binary — `yes` literally,
or halt. No "almost done," no partial fills, no commented-out completions.

**Role-protected gates.** Every phase gate declares its owner:
`owner: loop | user | external`. A `user`-owned gate (preloop_complete,
license clicks, secret install, identity decisions) cannot be flipped
to `yes` by the loop — only an explicit user edit or user message. If
the loop finds a user-owned gate set to `yes` without evidence, treat
it as `no`, restore the block, and continue only with non-gated
preparatory work. A `loop`-owned gate (e.g. `research_complete`) requires
structured `exit_evidence` — decision record, rejected alternatives,
assumptions still at risk, implications for RUBRIC.md and preloop.
Research may run at most 3 consecutive iterations without completing
the gate or naming a specific blocker in STATE.md.

If the project also has *open questions* requiring hands-on research (best
practices, model identity, runtime choice) before the preloop checklist
makes sense, add a **Phase 0: research** that *precedes* preloop, with its
own `research_complete: yes` gate. Phase order: research → preloop → bootstrap
(iter 0 automated) → iter 1+. Gate hardening applies to all phase transitions.

### 11. Per-iteration commit discipline from day one

Loops produce hundreds of durable artifacts (renders, frame-grabs,
transcripts, generated assets). The LEDGER and score files are durable
text; everything else is at risk until committed. Hard rule: every
iteration with file changes ends with `git add … && git commit` before
the iteration ends. Format: `chore(loop): iter NNN — <mode> —
<focus>`. Use `COMMIT_APPROVED=1` env to bypass interactive hooks.

## Halt-cause classifier

The emitted PROMPT.md must include this classifier. When the loop emits
`stop-and-summarize` or `escalate: <reason>`, label the cause:

- `derivation-gap` — blocked on something derivation could have asked
  for (model identity, budget cap, secret install, network/auth, …).
  The next derivation pass adds it to the Frontload audit so the loop
  doesn't block on it again.
- `genuine-escalate` — irreversible / external / authority-needed
  (paid API budget, public-publish, secrets, product direction with
  unclear rollback).
- `stone-converged` — legitimate completion; the artifact landed on
  the user's reframed target and further iteration has no positive
  yield.
- `signal-starvation` — quiet milestone region with no new evidence
  (no rubric revision, no consult finding, no user reframe) for the
  configured stretch.
- `wrong-loop` — the work has stopped being green-field (target now
  fixed and the loop should switch to `frontier-loop`, or the work is a
  known finite checklist better fit for `goal-loop`).

`derivation-gap` is the feedback signal: it tells the user the
Frontload checklist was incomplete; close it next run.

## Emit procedure

When invoked, perform these steps:

### 0. Pre-flight: Frontload audit

Before §1, walk this checklist. For each item: **resolve**
(AskUserQuestion if available, else print prominently in the
derivation response), **default + Alignment Review**, or
**escalate-mark**. Record in `loop/STATE.md` under `frontload:` and
prepend a brief frontload preamble to `loop/PROMPT.md` — exactly what
was resolved / defaulted / left as escalate candidates.

- **Target adjacency** — "X-adjacent" interpretation; INTENT.md
  hypotheses (≥3 live, see invariant 3).
- **Implied artifacts** — videos, designs, articles, code, slides, ….
- **Constraints** — solo dev, local-only, paid budget cap, deadline.
- **Model identity** — which LLM / which account / which physical
  machine.
- **Paid APIs** — list + budget caps (or `none-allowed`).
- **Secrets / credentials** — install paths; the user runs these, not
  the loop.
- **License clicks / hardware preconditions** — preloop checklist
  items (invariant 10).
- **Network / auth / VPN** — what the loop needs to reach.
- **Evaluator format** — RUBRIC.md v0.1 criteria draft (8–12).
- **Capability surface** — which tools the loop may install
  (invariant 6).
- **Consult availability** — frontier-model channel (Agentify / PAL /
  mcp / …); if absent, invariant 8 is marked `CONSULT unavailable` in
  PROMPT.md.
- **Cheap dignity test** — concrete pass/fail definition.
- **Audience-comprehension probe** — protocol for blind reads
  (invariant 2 extension).
- **Commit discipline** — `COMMIT_APPROVED=1` env (invariant 11).
- **Phase order** — research → preloop → bootstrap → iter 1+;
  ownership of each gate.

A derivation that doesn't close all of these — at least to defaults —
emits a frontload preamble naming the open gaps so the user can
decide before sleeping.

### Steps (after Pre-flight)

1. **Read the user's stated intent.** Extract: target adjacency
   ("X-adjacent"), implied artifacts (videos, designs, articles, code),
   constraints (solo dev, local-only, paid budget, …).
2. **Inspect the repo.** Is it empty? Half-scaffolded? Existing assets that
   bias the rubric? Note in STATE.md what's already there.
3. **Pick an initial rubric (8–12 criteria).** Cover at minimum: artifact
   clarity, narrative/structural coherence, density-vs-emptiness, pacing,
   and an "audience comprehension" axis. Concrete anchors per criterion.
4. **Write `loop/PROMPT.md`** with a **Runner contract** preamble
   (sibling skills share an identical block — keep in sync; copy from
   any `*-prompt-template.md` in the family), then all 11 invariants
   above, plus a capability-list specific to the project's domain.
   Invariant 7 carries the Judgment default. The prompt should be
   directly invokable by any runner — e.g.
   `/loop Read ./loop/PROMPT.md and follow its instructions.`,
   `/goal Read ./loop/PROMPT.md ...`, or fed to an external harness. The
   prompt is the same; the runner only differs in invocation.
5. **Write `loop/STATE.md`** with phase=0 (bootstrap), iteration=0, and
   `Next action: build RUBRIC.md v0.1 with concrete anchors and
   per-criterion evidence rules; do not render a production artifact until
   the rubric exists` (invariant 1 — evaluator scaffolding precedes
   artifact).
6. **Write `loop/README.md`** documenting how to fire, how to tune the
   rubric, how to halt, and what milestones look like.
7. **Print to user**: the one-liner to fire it, plus 2–3 sentences naming
   *which* of the 11 invariants are most likely to bite this specific
   project given its domain.

## Retrofit procedure (drifting existing loop)

When invoked on a drifting loop:

1. Read `loop/LEDGER.md` last 30 entries + current `loop/PROMPT.md` +
   `loop/RUBRIC.md` + `loop/STATE.md`.
2. Score against the 11 invariants — which are encoded? Which are missing?
   Which are encoded but the loop is violating them?
3. Identify the dominant failure mode (rubric inflation? escalate-polling?
   ceiling capture? capability stagnation?).
4. Emit a *minimal* PROMPT.md mutation that closes the failure mode —
   not a full rewrite. The hand-evolved prompt has hard-won lessons; the
   retrofit adds what's missing without losing them.
5. Write to `loop/STATE.md` a ⚠️ block telling the next iteration what
   changed and what to do differently.
6. **Skill Harvest.** If the retrofit teaches a lesson that generalizes
   beyond this run, write a Skill Harvest note: target skill · observed
   gap · evidence iteration · proposed rule · why it generalizes ·
   suggested patch wording · accidental-encouragement risk. Harvest notes
   are the dogfooding citations the family's promotion-bar rule requires
   (see `README.md` and the sibling templates).

## Anti-patterns

| Bad | Why | Good |
|-----|-----|------|
| Build the perfect rubric upfront | Rubric is a discovered artifact; perfecting it locks in wrong assumptions | Initial rubric in 30 minutes; revise every 5–15 iterations |
| Pick a numerical milestone and grind | Becomes a ceiling once hit | Milestone-shape (≥N criteria at 5, no <4) + imbalance-seeking |
| Let the loop pick its own creative direction | Will copy reference channels | Schedule frontier-model creative consults every ~10 iters |
| Trust loop self-scores | Inflation is the dominant early failure | Pixel/artifact citation mandatory for >2 |
| Write escalate permissively | Polling-shape for hours | Narrow escalate triggers + judgment-default + saturation rule |
| Commit only when human notices the file pile | Durability lag, lost work risk | Per-iteration commit discipline |
| Treat capability expansion as off-task | Green-field needs new tools | CAPABILITY mode at priority 0/1 |

## Provenance

This skill was extracted from a real project (a Kurzgesagt-adjacent video
engine, April 2026) that started with "build me something Kurzgesagt-adjacent"
and ran 230+ iterations through 8 phase-shifts. Every invariant above
corresponds to a failure the loop hit the hard way; the retro lives in that
project's `loop/META-RETRO.md` as anchored evidence.
