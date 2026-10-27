# Using the template

Two files, no build:

- `template.html` - the complete demo with the stylesheet inlined. Open it directly from disk; it makes no network requests.
- `template.css` - the same stylesheet, un-inlined. This is the copy-paste base for a real project.

The demo ships with the CSS inline so the single file stands alone. If you plan to edit the CSS, pick one source of truth:

```html
<!-- development: edit template.css, not the <style> block -->
<link rel="stylesheet" href="template.css">
```

Both files currently contain the same rules. If you change one, copy the change to the other (or replace the `<style>…</style>` block with the link above and stop inlining).

## Start a project

1. Copy `template.css` to your project (and `template.html` if you want the layout).
2. Keep the token block at the top intact.
3. Build the frame: `.shell` > `.masthead` + `.toolbar` + `.workspace` + `.creator-footer`.
4. Replace the demo content. Leave the component classes alone - the CSS is written against them.
5. Delete what you do not use (`.stat-*`, `.helper-panel`, the decoration section) rather than leaving it disabled.

No dependency, no reset, no normalize: the base block covers `box-sizing`, control font inheritance, focus and `[hidden]`.

## Swap a theme

```html
<html lang="en" data-class="ren">
```

- no attribute, or `data-class="hoyoung"` -> teal
- `data-class="ren"` -> rose
- `data-class="lynn"` -> green, +2px type offset, handwritten face if installed

At runtime:

```js
document.documentElement.dataset.class = 'ren';       // switch
delete document.documentElement.dataset.class;        // back to teal
```

Nothing else changes. If a theme needs a rule of its own, add it under the attribute selector - never branch in JS.

## Add a theme

Set these ten. That is the whole contract; the rest of the file inherits.

```css
:root[data-class="mint"]{
  --ui-accent:#7fd3b0;        /* identity accent */
  --ui-soft:#a7ddc9;          /* secondary text, tinted */
  --ui-bright:#e0f5ec;        /* selected text */
  --ui-active:#1f3a32;        /* selected fill, progress track */
  --ui-border:#4a6b5f;        /* control borders */
  --ui-panel-border:#3b4f47;  /* panel rules */
  --ui-page:#0f1513;          /* page */
  --ui-panel:#151d1a;         /* panel fill */
  --ui-panel-surface:rgba(21,29,26,.8);
  --ui-hover:#243830;         /* button hover */
}
```

Then, if the theme needs different data colours:

```css
:root[data-class="mint"]{--stat-main:#7fd3b0;--stat-additional:#8fa9d6}
```

Optional, only for a theme that should also resize: `--ui-font-size-offset:1px`, and `font-family` if you have a locally installed display face (always with the monospace fallback).

Before shipping a theme, check: body text `#d9d9d9` on `--ui-panel` stays above 7:1, `#999` above 4.5:1, and accent text on the panel above 4.5:1. Do not change the neutral hex values - they are shared across themes on purpose.

## Keep the highlight accent independent

`--skill-accent` is **not** part of a theme. Assign it per selected item so "the next thing" always looks different from "the product colour":

```js
document.documentElement.style.setProperty('--skill-accent', node.accent);
```

With no selection it falls back to `#a8b0ad`. What it drives:

- the 2px left spine on `.node-row` (every row, so the list reads as one family)
- the `.next` row fill (17% mix) and inset 2px outline
- the `.next-upgrade` border, 12% fill and its button border/hover (22%)

If your app has no per-item accent, give each theme its own highlight instead of reusing `--ui-accent`:

```css
:root[data-class="ren"]{--skill-accent:#e0b27c}
```

Never use `--skill-accent` on the masthead, toolbar or headings. That is what makes the highlight mean "look here".

## Decorations on or off

The two layers live at the end of `<body>`:

```html
<div class="decoration-layer decoration-back" aria-hidden="true" hidden></div>
<div class="decoration-layer decoration-front" aria-hidden="true" hidden></div>
```

- The `hidden` attribute is removed by the module when motion is allowed.
- The header button (`#animations`) toggles them, stores `hexa-ui-decorations=on|off` in `localStorage`, and reflects state in `aria-pressed` and its label.
- `prefers-reduced-motion: reduce` hides the layers and disables the button; the stored preference cannot override it.
- `document.hidden` adds `.is-paused`; CSS pauses all animations on the layer.

To remove decorations entirely: delete the two divs, the `#animations` button and the decoration block of the module (`const KEY` to `syncDecorations()`), then delete section 13 of the CSS. Nothing else references them.

To change the look without touching JS: adjust `--alpha`, `--size` ranges and the two counts in `build()`. Keep the accent colour as the particle colour - coloured confetti fights the accent budget.

## Add a panel

```html
<section class="panel" aria-labelledby="audit-title">
  <div class="panel-head">
    <h2 id="audit-title">Audit Log</h2>
    <span class="quiet">last 24h</span>
  </div>
  <p class="hint">Read-only view of recent writes.</p>
  <div class="table-wrap" tabindex="0" role="region" aria-label="Audit log">
    <table class="priority-table">...</table>
  </div>
</section>
```

Then place it:

- left column: add it inside `.inputs` (it stacks with a 12px gap automatically)
- right column: add it inside `.results` **and** wrap both panels in a `display:grid;gap:12px` container - `.results` is a single grid area
- full width: add a new row to `.workspace` and extend `grid-template-areas`

Checklist for a new panel:

- one `h2`, one border, `padding:13px 14px`, no margin
- numbers get `font-variant-numeric:tabular-nums` and a right-aligned cell
- an interactive element at the top gets `aria-label` if its text is a glyph
- any scrolling region gets `tabindex="0"`, `role="region"` and a label
- long content gets `min-width:0` on its grid parent

## Things that will break the look

- a `font-size` without `calc(... + var(--ui-font-size-offset))`
- a border radius above 3px (7px is reserved for the progress track)
- padding above 16px inside a panel
- a second accent hue, or accent-coloured headings and card backgrounds
- `1fr` without `minmax(0,...)` in a grid that contains a table
- a shadow on a panel
- motion that is not behind `prefers-reduced-motion` and a visible toggle
