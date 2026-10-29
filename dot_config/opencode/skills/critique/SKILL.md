---
name: critique
description: Adversarial design critique of the current work. Render it, look at it, and argue for rejection. Run after the gates are green, never instead of them.
license: MIT
compatibility: opencode
---
> Ported from the upstream Claude Code skill for opencode.
> Upstream ran the critic with `context: fork`. opencode has no such frontmatter,
> so the fresh context is the `design-critic` subagent: it never sees this
> conversation. That is the whole point, and it is what the steps below do.
> A leading `/name` refers to the skill of that name.

You are about to hand this work to a critic that cannot see the conversation
that produced it. Do not soften the handoff, and do not do the critic's job
first: a maker who has already argued for the work gets agreement, not review.

**Step 0: is the kit here?**

```bash
ls ~/.config/opencode/ux-ui-kit/tokens >/dev/null 2>&1 && echo KIT_OK || echo KIT_MISSING
```

On `KIT_MISSING` the kit was not installed. Say so plainly and stop. Do not
guess the contents of a file you could not open.

**Step 1: resolve the target.**

The target is the user's request: a file or a directory. If they named nothing
that exists, ask which file or directory to critique and stop. A critique of a
guessed target is worse than no critique, because it reads like a verdict on the
real work.

**Step 2: delegate to the critic, in a fresh context.**

Use the `subagent` tool with `agent: "design-critic"`. The delegation prompt must
carry everything the critic cannot otherwise know, because it sees none of this
conversation and cannot ask a question:

```
KIT=~/.config/opencode/ux-ui-kit
TARGET=<absolute path to the file or directory>
ORIGINAL REQUEST: <the user's own words, verbatim>

You cannot ask questions. Render the target at 1280 and 390 wide, light and
dark, then click every control. Report what you actually saw and what the
scripts actually printed. If you cannot render it, say so and review without
claiming otherwise.
```

Pass the `KIT` line verbatim: the critic has no skill directory of its own, so
that absolute path is the only way it can reach the scripts.

If `design-critic` is not available as a subagent, say so and stop rather than
critiquing the work yourself. A self-review from the context that made the work
is not a critique.

**Step 3: return the verdict as written.**

Give the user the critic's verdict, findings table and "what I could not judge"
unchanged. Do not re-rank the findings, do not remove the ones you disagree
with, and do not append your own defence. If the critic reports that it could not
render or could not judge something, that stays in the output.

**Step 4: state the scope, every time.**

This is judgement, not measurement. It produces no percentage, and no percentage
in this kit covers taste. Say so when you hand the verdict over.

---

## After the critique comes back

In this session, not in the fork:

Act on it. Fix every Critical and Major finding, re-run `gate`, and run this
critique again on what changed. The loop ends when the remaining findings are
Minor or Enhancement, not when you are tired of it.

Scoring the critic itself, rather than the work, is a maintainer task and lives
with the harness in the kit's repository - not here.
