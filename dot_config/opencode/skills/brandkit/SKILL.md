---
name: brandkit
description: Generate a complete, accessible brand design system from a brief, as primitive → semantic → component DTCG tokens (color, type, spacing, radius, shadow, motion), light + dark, plus a single theme.css, verified for WCAG. Use when the user wants a from-scratch brand/design foundation, a new palette + type system, or a themeable token kit for a product.
license: MIT
compatibility: opencode
---
> Ported from the upstream Claude Code skill for opencode.
> The kit is at `~/.config/opencode/ux-ui-kit`: every kit path below is written out in full.
> A leading `/name` refers to the skill of that name; where one is not
> installed, the text says what to do instead.


# Skill: Brand Kit

> **Step 0: is the kit here?** This skill reads files from the kit. Check once:
> `ls ~/.config/opencode/ux-ui-kit/tokens >/dev/null 2>&1 && echo KIT_OK || echo KIT_MISSING`
> On `KIT_MISSING` only the skill folders were installed, which is what
> `npx skills add` does. Say so plainly, point the user at
> `npx ux-ui-agent-skills init` or the plugin install, and stop. Do not guess the
> contents of a file you could not open.

Stand up the *foundation* (one token system everything renders from) before any screen. Get this right and every page stays consistent and themeable from one place.

## Steps
1. **Brief Inference (mandatory)**: name the industry, audience, the one mood adjective, and motion depth (`~/.config/opencode/ux-ui-kit/taste/design-taste.md` → Brief Inference). Pick an anchoring archetype from `~/.config/opencode/ux-ui-kit/taste/aesthetic-systems.md`.
2. **Primitives**: generate the brand color ramp in **OKLCH** (11 shades, consistent chroma) + a neutral ramp; verify the 500 shade ≥ 4.5:1 on white (text) and 600 ≥ 3:1 (UI) per `~/.config/opencode/ux-ui-kit/.claude/rules/tokens-and-color.md` → Color Generation.
3. **Semantic layer**: map roles to primitives (`action.primary`/`-hover`/`destructive`, `text.{primary,secondary,on-action,link}`, `surface.{page,card,raised}`, `border.{default,strong}`, `feedback.{success,warning,error,info}`), and the **dark** overrides (designed, not inverted).
4. **Scales**: Major Third type scale + composite text styles, 4px spacing scale, radius tiers, elevation, and `~/.config/opencode/ux-ui-kit/tokens/motion.json`-style durations/easings.
5. **Emit** the DTCG `~/.config/opencode/ux-ui-kit/tokens/*.json` (3-tier) + a single `theme.css` (the one shared source, `[data-theme="dark"]` overrides). Optionally feed the token-build pipeline (`token-build` skill) for other platforms.

## Verification (definition of done)
- `python3 ~/.config/opencode/ux-ui-kit/scripts/validate_tokens.py`: valid JSON, all aliases resolve.
- `python3 ~/.config/opencode/ux-ui-kit/scripts/validate_contrast.py`: required text/action/border pairs pass WCAG AA in **light AND dark**; `border.strong` ≥ 3:1.
- `python3 ~/.config/opencode/ux-ui-kit/scripts/validate_theme_refs.py`: every component `var(--…)` resolves to the theme.
- One theme, no per-page palettes; destructive = danger token (not primary); zero hardcoded values.

> Output is a verified token foundation: the measurable part is provable (run `npm run verify`). Brand "feel" still benefits from a human review against `~/.config/opencode/ux-ui-kit/taste/design-taste.md`.
