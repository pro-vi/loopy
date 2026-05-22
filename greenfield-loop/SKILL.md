---
name: greenfield-loop
description: "Derive a repo-specific prompt for an autonomous /loop in green-field territory - artifact, metric, and target all undefined at start. Distinct from /frontier-loop. Also retrofits a drifted loop."
---

# Greenfield Loop

This skill emits a repo-specific prompt for `/loop` (the persistent self-paced
harness) when the loop begins in **green-field territory** — empty repo,
vague creative intent, no pre-existing benchmark, no fixed evaluator.

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
+ initial `loop/STATE.md`. The loop runs the prompt; this skill writes it.

## When to invoke

Trigger on user phrases like:
- "build me something X-adjacent" (e.g. Kurzgesagt-adjacent, Notion-adjacent,
  Figma-adjacent — *adjacency* without specification is the green-field tell)
- "I want an agent that iterates toward Y" with Y under-specified
- "let's start a /loop on this empty repo"
- "make a content engine / design engine / writing engine that…"
- "I want to discover what the right shape of this product is"

Also invoke to **retrofit a drifting existing loop** that shows any of:
- Hourly AWAIT polling on user-gated questions
- Score milestones hit faster than expected, repeatedly
- Self-scoring bare/cosmetic outputs at high numbers
- 100+ untracked artifacts (no commit discipline)
- The user keeps reframing the target across sessions
- Loop hasn't tried anything *not* obviously copying a reference recently

## Do NOT invoke when

- Project has an explicit benchmark, oracle, CI signal, or pre-built evaluator → use `/frontier-loop`
- Single-issue closure work on existing code → use `/spark-loop`
- One-off planning (not iterative) → use `/scope`, `/atlas`, `/code-architect`
- Pure debugging → use `/debugging`

## What this skill produces

Three files, all in `loop/` at the repo root:

### 1. `loop/PROMPT.md`

The persistent prompt the user pastes into `/loop`. Carries the green-field
invariants below. **The prompt is the load-bearing artifact** — every commit
in this skill's lineage was driven by a failure mode that surfaced because
the prompt missed an invariant.

### 2. `loop/RUBRIC.md`

The initial measurement framework. Numbered criteria (typically 8–12 to
start), 0–5 scale, with concrete pixel/artifact-level anchors. Built to be
**revised as the artifact reveals itself** — the rubric is not a fixed truth.
Hard rule: every score above 2 requires citation evidence.

### 3. `loop/STATE.md`

Phase, iteration counter, current open seams, last action, and a `Next
action: HALT` exit hatch the user controls. Replaces itself as the loop
diagnoses imbalance each firing.

## The 11 green-field invariants (encoded in the emitted prompt)

These are the patterns that *every* successful green-field loop converges on
empirically. Encoding them up-front saves 50–100 iterations of rediscovery.

### 1. Evaluator scaffolding precedes artifact

The first 3–7 iterations are *rubric construction*, not output. The loop
authors `loop/RUBRIC.md`, picks initial criteria, writes concrete pixel/
artifact-level anchors, and only then renders the first artifact. If the user
demands output before evaluator, the prompt should warn that early outputs
will inflate.

### 2. Pixel/artifact-level evidence is mandatory for any score above 2

The single most reliable failure mode in green-field is *agent-grades-own-
homework*. The fix is structural: every score above 2 requires citation
evidence (extracted frames, AST nodes, test outputs, transcript spans —
whatever the artifact is). No citation = score caps at 2. Run a "would this
pass the cheap dignity test" guard (Keynote-slide-guard equivalent) before
writing the total.

### 3. The intent itself will change. Build for that.

In green-field, the user's "I want X-adjacent" is provisional. They will
look at output and reframe — typically 2–4 times before the actual target
locks in. Encode:
- Schedule explicit **user-look gates** every ~50 iterations (not hourly
  AWAITs — substantive review milestones)
- Treat any user reframe as a first-class iteration that may invalidate
  prior scores; write a `STATE.md` block that explicitly resets stale
  numbers when intent shifts
- Don't let the loop defend old work against new intent

### 4. Hard-coded numerical targets are scaffolding, not goals

Numerical milestones (45/50, 3 essays × 3 styles, etc.) prevent drift
early. They WILL become ceilings once hit. Encode:
- The rubric is replaceable; the philosopher's stone is not
- When milestone is hit "faster than expected," that's evidence the
  rubric was too easy — *raise the ceiling, don't celebrate*
- Once the user reframes to "no fixed milestone," switch to imbalance-
  seeking (see #5)

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

### 7. AWAIT is the most dangerous mode — restrict it severely

Permissive AWAIT collapses into hourly polling on questions the loop
should answer itself. Hard rule: AWAIT is for **paid APIs without budget
caps, public-publish actions, and secret prompts** — *nothing else*.
Topic, content, style, scene-direction, essay-text are all the loop's
job. Add a saturation rule: 2 consecutive AWAITs on the same logical
question force a default on the third.

### 8. Frontier-model consults are creative direction, not architecture review

Schedule a CONSULT every ~10 iterations. Frame the question as:
*"what's seductive-but-hollow about recent progress?"* and *"what omission
would look stupid in 6 months?"* — not "review my plan." Disagreement with
the consultant is allowed and sometimes correct. Capture into
`loop/creative-consults.md`.

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

- Idle on hourly AWAIT polling for things only the user can do
- Wrong-default decisions that should be the user's (which model? which tunnel?)
- Mix human-decision velocity with autonomous-iteration velocity, distorting
  scoring and pacing

When emitting the prompt, identify human-only items in the project's domain
and write them as a numbered preloop checklist with matching fields in
STATE.md. Add a **gate-hardening line**: the gate is binary — `yes` literally,
or halt. No "almost done," no partial fills, no commented-out completions.

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
scheduling next wake-up. Format: `chore(loop): iter NNN — <mode> —
<focus>`. Use `COMMIT_APPROVED=1` env to bypass interactive hooks.

## Emit procedure

When invoked, perform these steps:

1. **Read the user's stated intent.** Extract: target adjacency
   ("X-adjacent"), implied artifacts (videos, designs, articles, code),
   constraints (solo dev, local-only, paid budget, …).
2. **Inspect the repo.** Is it empty? Half-scaffolded? Existing assets that
   bias the rubric? Note in STATE.md what's already there.
3. **Pick an initial rubric (8–12 criteria).** Cover at minimum: artifact
   clarity, narrative/structural coherence, density-vs-emptiness, pacing,
   and an "audience comprehension" axis. Concrete anchors per criterion.
4. **Write `loop/PROMPT.md`** encoding all 10 invariants above plus a
   capability-list specific to the project's domain. The prompt should be
   directly invokable as `/loop Read ./loop/PROMPT.md and follow its
   instructions.`
5. **Write `loop/STATE.md`** with phase=0 (bootstrap), iteration=0, and
   `Next action: scaffold first artifact, populate initial RUBRIC anchors,
   render iteration-0 with no scoring`.
6. **Write `loop/README.md`** documenting how to fire, how to tune the
   rubric, how to halt, and what milestones look like.
7. **Print to user**: the one-liner to fire it, plus 2–3 sentences naming
   *which* of the 10 invariants are most likely to bite this specific
   project given its domain.

## Retrofit procedure (drifting existing loop)

When invoked on a drifting loop:

1. Read `loop/LEDGER.md` last 30 entries + current `loop/PROMPT.md` +
   `loop/RUBRIC.md` + `loop/STATE.md`.
2. Score against the 10 invariants — which are encoded? Which are missing?
   Which are encoded but the loop is violating them?
3. Identify the dominant failure mode (rubric inflation? AWAIT polling?
   ceiling capture? capability stagnation?).
4. Emit a *minimal* PROMPT.md mutation that closes the failure mode —
   not a full rewrite. The hand-evolved prompt has hard-won lessons; the
   retrofit adds what's missing without losing them.
5. Write to `loop/STATE.md` a ⚠️ block telling the next iteration what
   changed and what to do differently.

## Anti-patterns

| Bad | Why | Good |
|-----|-----|------|
| Build the perfect rubric upfront | Rubric is a discovered artifact; perfecting it locks in wrong assumptions | Initial rubric in 30 minutes; revise every 5–15 iterations |
| Pick a numerical milestone and grind | Becomes a ceiling once hit | Milestone-shape (≥N criteria at 5, no <4) + imbalance-seeking |
| Let the loop pick its own creative direction | Will copy reference channels | Schedule frontier-model creative consults every ~10 iters |
| Trust loop self-scores | Inflation is the dominant early failure | Pixel/artifact citation mandatory for >2 |
| Write AWAIT permissively | Hourly polling for hours | Narrow AWAIT triggers + saturation rule |
| Commit only when human notices the file pile | Durability lag, lost work risk | Per-iteration commit discipline |
| Treat capability expansion as off-task | Green-field needs new tools | CAPABILITY mode at priority 0/1 |

## Provenance

This skill was extracted from a real project (`/Users/provi/Development/_projs/stroke`,
April 2026) that started with "build me something Kurzgesagt-adjacent" and
ran 230+ iterations through 8 phase-shifts. Every invariant above corresponds
to a failure the loop hit the hard way. The retro lives at
`loop/META-RETRO.md` in that repo as anchored evidence.
