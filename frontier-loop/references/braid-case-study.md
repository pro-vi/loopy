# Braid Case Study

This skill was synthesized from a braid post-build branch that gradually turned into a review-closure ratchet.

## What Happened

The main oracle build had already landed the architecture. The remaining uncertainty lived in:

- runtime graph projection
- inspector filtering
- CLI operator behavior
- event-store activity ownership
- reviewed exact selectors that were not yet part of the default suite

The branch had two useful assets:

1. a reviewed ledger of closure slices
2. a rich explicit proof surface, including ignored exact selectors

That created a natural ratchet:

1. inspect the next reviewed slice
2. fix a real product bug if the proof exposed one
3. otherwise promote a stable exact probe into the default suite
4. record the closure in the ledger

## Good Outcome

This produced real value:

- important invariants moved from opt-in/manual coverage into ordinary test runs
- some promotions exposed genuine product bugs in runtime ownership, inspect filtering, CLI behavior, and event ordering

## Failure Mode

The branch eventually found a cheap local gradient:

- keep the ignored selector
- add a default-suite sibling
- validate both
- update the ledger

That is a valid ratchet, but it can become too locally greedy when the prompt does not force mode breaking.

## Lesson

Do not mistake "we have many reviewed probes left to promote" for "promotion is always the next best move."

Use the review-closure overlay only when it clearly dominates:

- product bug work
- evaluator blind-spot work
- observability work

If you can point to a real product defect or a known false-green, those outrank another narrow proof promotion.

The case study and review-closure overlay are intended to be self-contained.

## Second Drift: Observability-Echo (iters 56 → 69)

After this skill was re-derived to prevent the first drift, a second
gnhf run on braid produced 69 iterations under a freshly-authored
frontier-shaped prompt. Iters 1–56 were healthy: diverse interventions
across axes, ~34% new product capability, ramp regressions caught within
single-digit iterations, an explicit concentration-break at iter 16.

Iters 58–69 drifted into a tight observability-echo: 10/12 Display-impl
or activity-log summary-enrichment, 2/12 ramp repair (one caused by the
echo's own test-file drift), 0/12 new product capability. The loop's
own commit messages named the pattern — *"Extended the iter-48/49/52/
61/63/66 activity-log summary-enrichment family"* — with the family
reference list growing one hop per commit. Iter 64 explicitly said it
was "breaking the recent 3-of-5 summary-enrichment shape concentration"
and then resolved that diversity-signal into a rustfmt repair rather
than an axis-shift.

### Mechanism

Two causes compound here, and the second is load-bearing.

**Amplifier: commit-log priming.** The iteration protocol's step 1
asked the loop to "read recent commits on the current branch." Once a
narrow family dominated the window, priming data was the loop's own
drift, and the loop re-certified it as "what progress looks like here"
each iteration. The other step-1 anchors — ledger, failing selectors,
review findings — fail silent when the cheap oracle is green and no
review surface is active, so the recency window dominated. The
existing anti-collapse rules did not fire because they keyed on the
wrong dimensions: proof-promotion specifically (while the dominant
family was observability-echo), and file-level repetition (while the
loop distributed Display impls across different files and different
enums). Each commit looked locally distinct; only the intervention
*family* was the same.

**Root cause: admissibility.** Iter 64 is the tell. The loop
explicitly said in its commit message that it was *"breaking the
recent 3-of-5 summary-enrichment shape concentration"* — and then
discharged that requirement with a rustfmt repair. The pattern
information was present; the loop's diagnosis of concentration was
correct; what failed was the **menu of corrections the protocol
admitted as satisfying a mode break**. Cosmetic escape hatches were
admissible. That means the loop can drift on any prompt that reads
its own narrative — but it can also drift without any narrative
reading if the admissibility rule lets noticed concentration be
satisfied cheaply.

**Compounding condition: signal starvation.** Late in the run the
cheap oracle stayed green, no review surface was active, and no
external finding arrived. In that state the loop mines whatever
locally-admissible polish remains unless the protocol forces either
fresh signal or honest halt.

### Fix

The fix is narrower than the first-pass mutation. Initial instinct
was to remove commit-log reading from step 1 entirely and declare
the Ralph stateless w.r.t. predecessor narrative. Two independent
external reviews pushed back: that over-credits the priming
amplifier and under-credits the admissibility root cause, and it
also trades self-priming for amnesia (repo state does not encode
reverted attempts, dead hypotheses, or oscillation).

The landed fix has three parts:

1. **Demote commit narrative, don't ban it.** Step 1 now forbids
   recent commit messages as *positive generative evidence* for the
   next intervention; using them as a negative anti-repetition signal
   remains fine. Structured metadata (typed run artifacts, reviewed
   findings) is ranked above commit prose in the signal hierarchy.

2. **Add a forcing function with teeth (admissibility rule).** When
   same-family concentration is noticed, the next accepted change
   must either shift axis, cite a fresh failing trace / external
   finding / blocked claim, or halt. Cosmetic / rustfmt /
   file-rotation / naming-cleanup / ledger-only changes do **not**
   satisfy the mode break. This is the load-bearing fix; iter 64's
   rustfmt escape hatch is now an iteration to reject, not accept.

3. **Add a quiet-signal checkpoint.** After N green iterations with
   no new trace / finding, the loop runs the expensive outer channel
   or emits `stop-and-summarize`. Signal starvation no longer
   licenses indefinite polishing.

Memory-surface ranking was also added: externally reviewed findings
> typed run artifacts > self-authored ledger prose > commit log.
Anti-collapse rules key on the strongest available surface.

### Lesson (second)

Narrative recency priming amplifies drift, but the governing cause
is an admissibility rule that lets noticed concentration be
discharged by a cheap syntactic repair. A Ralph that can see the
pattern will still drift if the menu of corrections is too
permissive. Conversely, a Ralph that cannot see the pattern will
drift faster — but tightening priming without tightening
admissibility trades one failure mode for another.

Cross-iteration discipline the loop itself cannot be trusted to
apply (iter 64) belongs outside the loop: persisted findings,
external review passes, or — when the platform matures —
structured state like braid's event journal. In the meantime, the
protocol's own rules need enough teeth that admissible responses to
a diagnosed disturbance are genuinely corrective, not cosmetic.
