---
name: governance
description: Govern how the design system evolves (SemVer for tokens/components, the contribution workflow, deprecation policy, and change communication). Use when the user wants to add/promote/deprecate a component or token, decide a version bump, set up a contribution process, or keep the system from fragmenting.
license: MIT
compatibility: opencode
---
> Ported from the upstream Claude Code skill for opencode.
> The kit is at `~/.config/opencode/ux-ui-kit`: every kit path below is written out in full.
> A leading `/name` refers to the skill of that name; where one is not
> installed, the text says what to do instead.


# Skill: Governance

> **Step 0: is the kit here?** This skill reads files from the kit. Check once:
> `ls ~/.config/opencode/ux-ui-kit/tokens >/dev/null 2>&1 && echo KIT_OK || echo KIT_MISSING`
> On `KIT_MISSING` only the skill folders were installed, which is what
> `npx skills add` does. Say so plainly, point the user at
> `npx ux-ui-agent-skills init` or the plugin install, and stop. Do not guess the
> contents of a file you could not open.

Keep the system consistent as it grows. Apply versioning, contribution, and deprecation rules.

## Steps
1. Read `~/.config/opencode/ux-ui-kit/workflows/governance.md` (SemVer table, contribution workflow, deprecation policy, change comms).
2. Classify the change: **major** (breaking: renamed/removed token or prop, changed anatomy/default), **minor** (additive: new token/component/variant/optional prop), **patch** (fix: contrast/bug/doc/value tweak in tolerance).
3. For a **new** component/token: confirm it serves a real, repeated need (≥ 2 places) before promoting product → candidate → core. Design it to the full quality bar (`~/.config/opencode/ux-ui-kit/.claude/rules/components.md` → Component Quality Bar).
4. For a **deprecation**: mark with reason + replacement + removal version; keep working ≥ 1 minor cycle; provide a migration map (`~/.config/opencode/ux-ui-kit/design-systems/crosswalk.md` style); remove only in a major.
5. Wire any new file into `AGENTS.md` (File Reference Map + relevant table/router) and add a changelog entry.

## Verification (definition of done)
- Change has a SemVer level **and** a changelog entry.
- Removals have a deprecation window, a replacement, and a migration table.
- New spec meets the 8-state + a11y + token-mapping bar and is reachable via the router.
- `python3 ~/.config/opencode/ux-ui-kit/scripts/validate_tokens.py` passes; contrast re-checked if colors changed.
