---
name: image-to-code
description: Turn a reference image, screenshot, or mockup into token-driven, accessible code. Infer the design system from the reference (palette, type scale, spacing, radius, layout archetype), map it to the 3-tier tokens, rebuild it, then verify with the kit's gates. Use when the user provides a design/screenshot and wants matching UI code.
license: MIT
compatibility: opencode
---
> Ported from the upstream Claude Code skill for opencode.
> The kit is at `~/.config/opencode/ux-ui-kit`: every kit path below is written out in full.
> A leading `/name` refers to the skill of that name; where one is not
> installed, the text says what to do instead.


# Skill: Image to Code

> **Step 0: is the kit here?** This skill reads files from the kit. Check once:
> `ls ~/.config/opencode/ux-ui-kit/tokens >/dev/null 2>&1 && echo KIT_OK || echo KIT_MISSING`
> On `KIT_MISSING` only the skill folders were installed, which is what
> `npx skills add` does. Say so plainly, point the user at
> `npx ux-ui-agent-skills init` or the plugin install, and stop. Do not guess the
> contents of a file you could not open.

Reconstruct a design from a visual reference as a real design system, not a one-off copy. Match the *system* (color/type/spacing language), never lift copyrighted imagery or brand assets.

## Steps
1. **Read the reference like a designer.** Infer and write down:
   - **Palette**: 1 dominant surface family, text colors, 1 primary action + at most 1 accent (sample the hues; don't guess random hex).
   - **Type**: family feel (geometric/grotesk/serif), the scale jumps, display vs. body contrast, weights.
   - **Spacing & density**: base unit, section rhythm, card padding; airy vs. compact.
   - **Radius & depth**: radius language (sharp/soft/pill), shadow vs. hairline separation.
   - **Layout archetype + sequence**: bounded hero / asymmetric split / dense bento / editorial stack (`~/.config/opencode/ux-ui-kit/taste/design-taste.md` → Variance Mandate).
2. **Anchor to a known system** if it's close: browse `~/.config/opencode/ux-ui-kit/taste/aesthetic-systems.md` / `python3 ~/.config/opencode/ux-ui-kit/scripts/design_systems.py search <term>` and adopt that recipe to stabilize decisions.
3. **Build the token theme** from the inferred values → 3-tier DTCG (`design-tokens` skill); generate a single `theme.css`. Verify every color pair with `~/.config/opencode/ux-ui-kit/scripts/contrast.py` / `~/.config/opencode/ux-ui-kit/scripts/validate_contrast.py` (light + dark). A sampled brand color that fails AA gets adjusted; taste never overrides POUR.
4. **Rebuild layout + components** token-driven via `~/.config/opencode/ux-ui-kit/frameworks/adapter-protocol.md` + `~/.config/opencode/ux-ui-kit/components/*`: one shared primitive layer, all 8 states, a11y wired, no emoji (lucide), single theme. Apply taste (`design-taste.md`) so it doesn't regress to generic.
5. **Verify against the reference**: render and screenshot it, compare side-by-side to the reference; run `node ~/.config/opencode/ux-ui-kit/scripts/gate.mjs measure_render.mjs`, `lint_hardcodes.py`, `taste_audit.mjs`, and `npm run verify`.

## Verification (definition of done)
- `npm run verify` is 100% (tokens resolve, contrast AA light+dark, no hardcodes/emoji, real-render WCAG).
- The rebuilt UI uses ONE inferred token theme; no per-section palettes.
- A screenshot of the result visibly matches the reference's design language.

> Honest limit: this matches the design **system**, not a pixel-perfect copy. Do not reproduce the reference's photographs, logos, or copyrighted copy. Substitute your own or generic placeholders.
