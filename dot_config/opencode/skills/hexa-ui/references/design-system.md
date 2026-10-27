# Design system reference

Everything here is extracted from the reference implementation and ships in `assets/template.css`. Values are literal: if a number is not in this file, it is not part of the system.

## 1. The two-accent contract

The system has **two** accent variables with different jobs. This is the core trick.

| Variable | Job | Where it may appear |
|---|---|---|
| `--ui-accent` | Theme identity | kicker, focus ring, progress fill, gain text, ghost-button border/text, selected control border, checkbox, links |
| `--skill-accent` | The one highlighted object | `.next` row fill + 2px outline, 2px left spine on node rows, `.next-upgrade` border + fill, its button border/hover |
| `--stat-main`, `--stat-additional` | Data series | segmented bars only |

`--skill-accent` has a fallback of `#a8b0ad` and is expected to be **assigned per selected item** at runtime, not per theme:

```js
document.documentElement.style.setProperty('--skill-accent', node.accent);
```

Rules:

- Theme accent never fills a whole row or card; it is a line, a border, a 7px bar or text.
- Highlight accent never touches chrome (masthead, toolbar, footer, headings). Only the next object.
- Stat colours are independent of both, so three data series stay distinguishable after a theme swap.
- If you need a per-theme highlight (rose UI, warm highlight), set `--skill-accent` inside the theme block. Do not reuse `--ui-accent` for it - that collapses the contract.

```css
/* Optional per-theme highlight, only if the app has no per-item accent. */
:root[data-class="ren"]{--skill-accent:#e0b27c}
```

## 2. Theme tokens

Ten variables make a theme. The remaining three are behavioural.

| Token | Default (teal) | Role |
|---|---|---|
| `--ui-accent` | `#85c8c3` | identity accent: kicker, focus, progress, gain, ghost buttons, selected borders |
| `--ui-soft` | `#a7d8d4` | secondary labels: completion label, version, priority context, stat summaries |
| `--ui-bright` | `#d8eeeb` | selected-control text, strongest values, version name |
| `--ui-active` | `#233a39` | fill behind a selected/pressed control; progress track |
| `--ui-border` | `#425b59` | interactive borders (selects) |
| `--ui-panel-border` | `#3d4948` | panel borders, masthead rule, footer rule, toast border |
| `--ui-page` | `#111414` | page background |
| `--ui-panel` | `#161a1a` | panel fill; dialog fill |
| `--ui-panel-surface` | `rgba(22,26,26,.8)` | translucent panel fill when decoration must show through |
| `--ui-hover` | `#263231` | button hover fill |
| `--skill-accent` | `#a8b0ad` | independent highlight (section 1) |
| `--stat-main` | `#79c8bd` | primary segmented bar |
| `--stat-additional` | `#83a9ca` | secondary segmented bar |
| `--ui-font-size-offset` | `0px` | the only type-scale knob |

### Neutrals are deliberately not tokens

The neutral ladder stays fixed across themes so a theme changes hue, not contrast. Use these literals:

| Value | Use |
|---|---|
| `#d9d9d9` | base text |
| `#e2e2e2` | control text |
| `#bfbfbf` | body copy in long panels |
| `#bcbcbc` | group labels |
| `#aaa` | field labels, muted table head, secondary controls |
| `#999` | hints, quiet notes, metric labels |
| `#858585` | group counts, dim secondary |
| `#747474` | zero values |
| `#444` | control border |
| `#3d4847` | empty stat-bar cell border |
| `#202626` | empty stat-bar cell |
| `#303633` / `#747d78` / `#dce4de` | tag fill / tag border / tag text |
| `#303a32` / `#b9cfbd` | node-icon fill / glyph |
| `#292929` | row rule |
| `#333`, `#383838`, `#3d4948` | separators inside panels, table frame |
| `#222` | sticky table head |
| `#1a1a1a` | zebra row |
| `#1b1b1b` | unselected stat tile |
| `#1d1d1d` | control fill |

## 3. Theme recipes

Copy-paste. Teal is the root default; the attribute form is an alias so the theme can also be selected by `data-class`.

```css
/* hoyoung / teal (default) */
:root,:root[data-class="hoyoung"]{
  --ui-accent:#85c8c3;--ui-soft:#a7d8d4;--ui-bright:#d8eeeb;
  --ui-active:#233a39;--ui-border:#425b59;--ui-panel-border:#3d4948;
  --ui-page:#111414;--ui-panel:#161a1a;--ui-panel-surface:rgba(22,26,26,.8);
  --ui-hover:#263231;
  --stat-main:#79c8bd;--stat-additional:#83a9ca;
}

/* ren / rose */
:root[data-class="ren"]{
  --ui-accent:#d68b99;--ui-soft:#e4adb7;--ui-bright:#f3dfe4;
  --ui-active:#3d272d;--ui-border:#78505a;--ui-panel-border:#4b3d40;
  --ui-page:#151113;--ui-panel:#1b1719;--ui-panel-surface:rgba(27,23,25,.8);
  --ui-hover:#35272c;
  --stat-main:#d78b9c;--stat-additional:#b89bcf;
}

/* lynn / green - also raises the whole type scale and borrows a handwritten face */
:root[data-class="lynn"]{
  --ui-accent:#a4d89c;--ui-soft:#a7ded1;--ui-bright:#e0f2dd;
  --ui-active:#2b4636;--ui-border:#557965;--ui-panel-border:#405e50;
  --ui-page:#101b17;--ui-panel:#17271f;--ui-panel-surface:rgba(23,39,31,.9);
  --ui-hover:#2c4437;
  --stat-main:#a4d89c;--stat-additional:#b3afd9;
  --ui-font-size-offset:2px;
  font-family:"Patrick Hand","Segoe Print","Bradley Hand",ui-monospace,SFMono-Regular,Consolas,monospace;
}
```

`lynn` is the proof that `--ui-font-size-offset` works: +2px scales every rule in the file at once. The display face is referenced by name with a full fallback stack - never ship or fetch a font file; if the user has it installed they get it, otherwise the monospace stack takes over.

## 4. Type scale

Base `font-size: calc(13px + var(--ui-font-size-offset))`. Nothing sets a raw size.

| Role | Size | Notes |
|---|---|---|
| base / body / labels | `calc(13px + offset)` | inherited |
| h1 | `calc(25px + offset)` | `letter-spacing:.01em`, `line-height:1.15` |
| h2 | `calc(15px + offset)` | weight 700, margin 0 |
| dialog h2 | `calc(18px + offset)` | |
| completion value | `calc(16px + offset)` | `--ui-bright` |
| totalling strong / estimate | `calc(14px + offset)` | |
| table | `calc(12px + offset)` | `tabular-nums` |
| table on ≤450px | `calc(11px + offset)` | |
| kicker | `calc(11px + offset)` | uppercase, `letter-spacing:.13em`, accent |
| hint / quiet / labels | `calc(11px + offset)` | `line-height:1.4` |
| metric label / mini bars | `calc(10px + offset)` | uppercase, `letter-spacing:.04em` |
| skill tag | `calc(10px + offset)` | weight 600 |

Font stack, exactly:

```css
font-family:ui-monospace,SFMono-Regular,Consolas,'Liberation Mono',monospace;
```

No second family, no web fonts, no icon font. Glyphs in `node-icon` are text (`M4`, `S1`, `C2`).

## 5. Spacing and density

| Place | Value |
|---|---|
| shell padding | `20px 20px 48px` (max-width 1320px, centred) |
| masthead bottom padding | `15px` |
| toolbar padding / gap | `16px 0` / `14px` |
| workspace gap | `12px` |
| panel padding | `13px 14px` |
| panel-head gap | `12px` |
| node row | `min-height:34px`, `padding:3px 4px`, `gap:8px` |
| node level input | `width:52px`, `padding:3px 2px`, centred |
| table cell | `padding:5px 8px`, `height:29px` |
| table head | `padding:7px 8px` |
| button | `padding:4px 9px` (min-height 32px for actions) |
| input / select | `padding:4px 6px` |
| stat tile | `min-height:113px`, `padding:8px 5px`, `gap:8px` |
| footer | `margin-top:20px`, `padding-top:16px`, gap `12px` |

Repeated gaps: 1 (inside a segmented bar), 2 (between segmented controls), 4, 5, 6, 8, 10, 12, 14, 16, 20. Avoid anything between - it breaks the rhythm.

## 6. Borders, radii, elevation

- Widths: **1px** everywhere. The only 2px borders are the highlight spine (`border-left`) and the `.next` row outline (drawn inside with `outline-offset:-2px`).
- Radii: **2px** controls and panels-adjacent boxes, **3px** tags, **7px** progress track, **50%** status dot. Nothing else. No rounded cards, no pill buttons, no drop shadows on panels.
- The only shadows: floating toast (`0 3px 12px #0008`) and the helper panel (`0 8px 24px #0009`).
- Panels are flat: `border:1px solid var(--ui-panel-border);background:var(--ui-panel)`. Depth comes from the lighter fill, not from elevation.

## 7. Colour usage rules

| Need | Use |
|---|---|
| Page background | `--ui-page` |
| Panel / dialog fill | `--ui-panel` |
| Fill that must reveal decoration | `--ui-panel-surface` |
| Divider between panels and page | `--ui-panel-border` |
| Divider inside a panel | `#292929` (rows), `#333` (sections), `#444` (stronger) |
| Selected / pressed fill | `--ui-active` |
| Selected text | `--ui-bright` |
| Secondary label | `--ui-soft` (accent-tinted) or `#aaa` (neutral) |
| Disabled / completed | `opacity:.45`, never a new grey |
| Positive / saved | `#58c98c` |
| Pending / saving / conflict | `#e8bf63` |
| Offline / error | `#e77979` |

Do not use `--ui-accent` as a large background. The largest accent surface in the system is the `.stat-selector.is-selected` fill, and that uses `--ui-active`, not the accent.

## 8. Emphasis with `color-mix`

The highlight is never a new hex value; it is the highlight accent mixed into the panel tone.

```css
/* the next row (17%) */
.priority-table tr.next{
  background:#242725;
  background:color-mix(in srgb,var(--skill-accent) 17%,#1a1a1a);
}
.node-row.next{background:color-mix(in srgb,var(--skill-accent) 17%,#1a1a1a)}

/* the next-upgrade card (12%) */
.next-upgrade{
  border:1px solid var(--skill-accent);
  background:#202220;
  background:color-mix(in srgb,var(--skill-accent) 12%,#161616);
}

/* accent button hover (22%) */
.upgrade-cost button:hover{background:color-mix(in srgb,var(--skill-accent) 22%,#252525)}

/* translucent highlight over a decorated page (from the reference) */
background:color-mix(in srgb,color-mix(in srgb,var(--skill-accent) 17%,var(--ui-panel)) 80%,transparent);
```

Always write the flat fallback first, then `color-mix`. Percentages are fixed: 12% card, 17% row, 22% hover. Keep them; a 30% row stops reading as a highlight and starts reading as a state.

The `.next` row also gets a solid 2px outline in the same accent, inset by 2px so the row height does not change:

```css
outline:2px solid var(--skill-accent);outline-offset:-2px;
```

## 9. Status and progress

- Progress track: 7px tall, `--ui-active`, radius 7px; fill `--ui-accent`, width via `--value`.
- Status dot: 7px circle, saved `#58c98c`, saving `#e8bf63`, error `#e77979`.
- Completed table row: `opacity:.45` plus the `.hide-done` table class to remove it entirely.
- Long-running state is text + dot, never a spinner animation alone (and no spinner under reduced motion).

## 10. Accessibility constants

```css
input:focus,select:focus,button:focus-visible,summary:focus-visible,[tabindex]:focus-visible{
  outline:1px solid var(--ui-accent);outline-offset:1px;
}
.visually-hidden{position:absolute;width:1px;height:1px;padding:0;margin:-1px;overflow:hidden;clip:rect(0,0,0,0);white-space:nowrap;border:0}
@media(pointer:coarse){/* every actionable control: min-height:44px */}
```

Contrast against `#161a1a`: `#d9d9d9` ~12.5:1, `#aaa` ~7.5:1, `#999` ~6.2:1, `#858585` ~4.8:1. Do not go dimmer than `#858585` for text that carries meaning. Accent text on the panel is ~9:1 for teal, ~6.7:1 for rose, ~10.7:1 for green - verify any new theme before shipping.
