---
name: hexa-ui
description: Dark monospace "data terminal" UI design system extracted from the HEXA Matrix Tracker - themeable CSS variable contract (--ui-* / --skill-accent), compact panel and table components, decorative particle layer, and a runnable HTML/CSS template. Use when building dark developer tools, game trackers, dashboards, or any dense monospace interface with a game-like accent theme.
---

# HEXA UI

A dependency-free dark terminal look: monospace type, hairline panels, near-square corners, cramped data density, one theme contract and a second independent highlight accent. No framework, no build step, no network assets.

Good for dashboards, trackers, admin consoles, local tools and dense settings pages. Wrong for marketing pages, airy consumer apps and anything touch-first at large sizes (the density assumes a mouse).

## The seven rules

1. **One theme contract, two accents.** A theme is ~10 `--ui-*` variables on `:root[data-class="..."]`. `--skill-accent` is separate: it marks the single "next" row, card and spine. Theme accent = identity; highlight accent = one object. Never merge them.
2. **Scale type with `--ui-font-size-offset`, never per element.** Every size is `calc(Npx + var(--ui-font-size-offset))` with the offset on `:root` (default `0px`). One variable resizes the whole UI; a theme can set `2px` and nothing else changes.
3. **Cramped, terminal-like density.** Table rows are 29px, node rows 34px tall, buttons `4px 9px`, inputs `4px 6px`. Resist "breathing room": the tightness is the style.
4. **Near-square, hairline.** `border-radius:2px` on controls (3px on tags, 7px only on the progress track). Borders are 1px and quiet; panels use one border, not shadows.
5. **Tabular numerals for anything numeric.** `font-variant-numeric:tabular-nums` on tables, totals, costs, levels, gains. Right-align numeric table columns.
6. **Motion is decoration: optional, toggleable, reduced-motion aware.** It lives in fixed, `pointer-events:none`, `aria-hidden` layers behind and in front of the panels, never in the layout. It can be turned off without losing meaning.
7. **Accent means something.** Teal/pink/green appears on the kicker, the focus ring, the current progress, the next upgrade and gain values - not on random headings, icons or card backgrounds. If everything is accented, nothing is.

## Token contract

```css
:root{
  --ui-accent:#85c8c3; --ui-soft:#a7d8d4; --ui-bright:#d8eeeb;
  --ui-active:#233a39; --ui-border:#425b59; --ui-panel-border:#3d4948;
  --ui-page:#111414; --ui-panel:#161a1a; --ui-panel-surface:rgba(22,26,26,.8);
  --ui-hover:#263231;
  --skill-accent:#a8b0ad;
  --stat-main:#79c8bd; --stat-additional:#83a9ca;
  --ui-font-size-offset:0px;
}
```

Full reference, all three theme recipes and the `color-mix` emphasis values: `references/design-system.md`.

## Quick start

1. Copy `assets/template.css` next to your page, or start from `assets/template.html` (CSS already inlined, works offline from `file://`).
2. Keep the token block at the top. Everything else in the file reads from it.
3. Pick a theme with `<html data-class="ren">` (or `lynn`); no attribute = teal. To switch at runtime: `document.documentElement.dataset.class = 'ren'`.
4. Assemble the page: `.shell` > `.masthead` + `.toolbar` + `.workspace` + `.creator-footer`. The workspace is the two-column grid; see `references/layout-and-motion.md`.
5. Copy components from `references/components.md` - each one carries its markup, CSS and required ARIA.
6. Add the decoration layer last and keep it off the layout path (`references/layout-and-motion.md`, optional section).

## File map

| File | Contents |
|---|---|
| `assets/template.html` | Runnable single-file demo: masthead, toolbar, summary, node matrix, stat tracks, priority table with a working expand row, calculator, footer, theme switch, decoration toggle |
| `assets/template.css` | The same stylesheet, un-inlined - the copy-paste base |
| `assets/README.md` | How to reuse, retheme, add a panel, turn decorations off |
| `references/design-system.md` | Every token, theme recipe, type/spacing scale, colour rules, `color-mix` recipes |
| `references/components.md` | 18 component recipes with real values and ARIA |
| `references/layout-and-motion.md` | Grid shell, breakpoint ladder, stacking order, particle layer |

## Before shipping

- [ ] No font sizes outside `calc(... + var(--ui-font-size-offset))`.
- [ ] No new accent colour: an accent need is `--ui-accent` or `--skill-accent`.
- [ ] Tables have `font-variant-numeric:tabular-nums`, sticky `th`, right-aligned numbers.
- [ ] Focus is visible (`outline:1px solid var(--ui-accent)`), toggles use `aria-pressed`, live regions use `role="status"` (which implies `aria-live="polite"`).
- [ ] Decoration layer is `aria-hidden`, `pointer-events:none`, hidden under `prefers-reduced-motion`, and pausable.
- [ ] Controls are 44px minimum on `pointer:coarse`. Check the computed size, not the CSS: an `id` selector (e.g. `#animations`) outranks the class list in the media query, so size-critical controls must use `var(--header-control-height)` rather than a hard-coded `min-height`.
- [ ] It still reads as an interface when every decoration is removed.
