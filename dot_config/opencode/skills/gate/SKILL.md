---
name: gate
description: Run the one-command quality gate and report the real N/N result. Use before claiming any build/review is done.
license: MIT
compatibility: opencode
---
> Ported from the upstream Claude Code skill for opencode.
> The kit is at `~/.config/opencode/ux-ui-kit`: every kit path below is written out in full.
> A leading `/name` refers to the skill of that name; where one is not
> installed, the text says what to do instead.

> **Step 0: is the kit here?** This skill reads files from the kit. Check once:
> `ls ~/.config/opencode/ux-ui-kit/tokens >/dev/null 2>&1 && echo KIT_OK || echo KIT_MISSING`
> On `KIT_MISSING` only the skill folders were installed, which is what
> `npx skills add` does. Say so plainly, point the user at
> `npx ux-ui-agent-skills init` or the plugin install, and stop. Do not guess the
> contents of a file you could not open.

Run the full quality gate and report the ACTUAL output, never a remembered or
reasoned number.

1. Run: `node ~/.config/opencode/ux-ui-kit/scripts/accuracy_report.mjs`
   (tokens + contrast + spec + no-hardcode + theme-refs + no-emoji +
   real-render WCAG + state-aware, light and dark; all-or-nothing).
2. Report the exact `N/N` line it prints.
3. If it is not fully green:
   - List each failing check verbatim.
   - Fix the cause (do not suppress the check).
   - Re-run until green. Do not announce success between failures.
4. For any rendered HTML touched this session, also run the pixel-truth checks
   the gate cannot prove:
   - `node ~/.config/opencode/ux-ui-kit/scripts/gate.mjs verify_states.mjs <file> [--dark]`
   - `node ~/.config/opencode/ux-ui-kit/scripts/gate.mjs verify_responsive.mjs <file>`
   - RENDER-AND-LOOK: screenshot the harness, park the pointer off it, inspect
     every state, click each control to confirm the state changed.

Honest scope: these gates prove objective correctness (tokens, a11y, no drift).
They do not prove taste. Pair with `DS_REQUIRE_BROWSER=1 node ~/.config/opencode/ux-ui-kit/scripts/taste_audit.mjs` + human review;
never claim auto-100% on aesthetics.
