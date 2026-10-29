---
name: ship
description: Pre-release gate. Run the full gate, responsive + render checks, then produce the release checklist (README badge/current/changelog). Use before tagging a release.
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

Gate first, then prepare the release. Do nothing destructive without explicit
confirmation (no tag, no publish). This command verifies and drafts only.

1. Quality gate (must be fully green before continuing):
   - `node ~/.config/opencode/ux-ui-kit/scripts/accuracy_report.mjs`: report the real N/N.
   - `node ~/.config/opencode/ux-ui-kit/scripts/verify_responsive.mjs examples`: no overflow from 280 to 951px, hinge clear when folded.
   - `python3 ~/.config/opencode/ux-ui-kit/scripts/check_no_emoji.py`: UI + taste + instruction surface.
   If anything fails, stop and fix; do not proceed to the checklist.

2. Release checklist (per the project release rule):
   - Update `README.md`: version badge, "current" line, and changelog entry.
   - Confirm the bump level (major/minor/patch) with the user. It is their call.
   - Confirm `examples/apple-home` stays gitignored (local IP).
   - List the commits since the last tag so the changelog is accurate.

3. Output a ready-to-review summary: the green N/N line, the proposed version,
   and the drafted changelog entry. Wait for the user to approve before any
   `git tag` / publish step.
