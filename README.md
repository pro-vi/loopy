# loopy

Prompt generators for `/goal` and `/loop`.

`loopy` is a small family of Claude Code skills that **derive repo-specific
prompts for autonomous improvement loops**. The skills do not run the loop —
they author the prompt (and the invariants) that an external harness or
`/loop` then executes.

## The skills

| Skill | Derives a loop that… | Use when |
|---|---|---|
| `frontier-loop` | moves a repo's evidence-backed capability frontier | an artifact/evaluator exists and should get better |
| `greenfield-loop` | discovers what to build when nothing is defined | empty repo, vague "build me something X-adjacent" intent |
| `spark-loop` | seals one operator-facing seam per iteration | a known, closure-shaped seam with a cheap proof surface |
| `story-loop` | maintains a living product storyboard | discovering / verifying user-facing product promises |

Rough routing: **target undefined →** `greenfield-loop` · **move an existing
frontier →** `frontier-loop` · **close one known seam →** `spark-loop` ·
**product promises →** `story-loop`.

## Install

Clone, then symlink the skills into your agent's skills directory:

```sh
git clone git@github.com:pro-vi/loopy.git
for s in frontier-loop greenfield-loop spark-loop story-loop; do
  ln -s "$PWD/loopy/$s" ~/.claude/skills/$s      # Claude Code
  ln -s "$PWD/loopy/$s" ~/.codex/skills/$s       # Codex (optional)
done
```

Each skill is a plain directory with a `SKILL.md` — no plugin runtime, so it
works with any agent that reads skill folders.

## Notes

See [`REVIEW.md`](REVIEW.md) for the most recent frontier-model design review
of these skills and what changed because of it.
