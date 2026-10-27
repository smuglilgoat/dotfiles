# Layout and motion

## 1. The shell

```html
<main class="shell">
  <header class="masthead">...</header>
  <div class="toolbar">...</div>
  <div class="workspace">...</div>
  <footer class="creator-footer">...</footer>
</main>
```

```css
.shell{position:relative;max-width:1320px;margin:0 auto;padding:20px 20px 48px}
```

- 1320px cap, 20px gutters, generous 48px bottom padding so the toast never sits on the last panel.
- The masthead owns the only full-width horizontal rule; the toolbar has no border.
- `position:relative` makes `.shell` the anchor for the z-index ladder (section 4).

## 2. The workspace grid

```css
.workspace{
  display:grid;
  grid-template-columns:minmax(0,1.3fr) minmax(390px,1fr);
  grid-template-areas:"inputs results" "quickstats results";
  gap:12px;
  align-items:start;
}
.inputs{grid-area:inputs;display:grid;gap:12px;align-content:start;min-width:0}
.results{grid-area:results;min-width:0}
.quickstats{grid-area:quickstats;display:grid;gap:12px;align-content:start;min-width:0}
```

Why this shape:

- **Two columns, three areas.** Inputs and the small calculator stack on the left; the results column spans both rows on the right. That keeps the table tall and the form narrow, which is the natural shape of the content.
- **`minmax(0,Xfr)`, never `1fr`.** Grid items default to `min-width:auto`; a long table or an unwrapped name would push the column wider than the viewport. `minmax(0,...)` plus `min-width:0` on every grid child is what actually prevents horizontal overflow.
- **`minmax(390px,1fr)`** is the smallest width at which the 5-column priority table still reads. Below that, the layout must collapse rather than squeeze.
- **`align-items:start`** so a short column does not stretch its panels.
- Panels carry no margin; the 12px `gap` is the entire vertical rhythm.

Sub-grids use the same rule: `.node-list{grid-template-columns:repeat(2,minmax(0,1fr))}`, `.stat-selectors{repeat(3,minmax(0,1fr))}`, `.totals{repeat(2,minmax(0,1fr))}`.

### View switching (optional)

One attribute on `.workspace` swaps the whole layout, no JS beyond setting it:

```css
.workspace[data-view="priority"]{grid-template-columns:minmax(0,1fr);grid-template-areas:"results"}
.workspace[data-view="priority"]>.inputs,.workspace[data-view="priority"]>.quickstats{display:none}
.workspace[data-view="cost"]{grid-template-columns:minmax(0,1fr);grid-template-areas:"quickstats"}
.workspace[data-view="cost"]>.inputs,.workspace[data-view="cost"]>.results{display:none}
```

Attribute selectors out-specify the breakpoint rules, so the view survives a resize. Better than toggling `hidden` on every panel from JS.

## 3. Breakpoint ladder

Six steps, each with one job. Do not add a seventh without a reason.

| Width | What changes |
|---|---|
| **1200px** | Columns rebalance: `minmax(0,1.15fr) minmax(360px,1fr)`. Still two columns. *This step is a template addition, not extracted from the reference implementation - drop it if you want the original ladder.* |
| **1000px** | The workspace becomes one column, areas `"inputs" "quickstats" "results"` - the long table goes last. `.shell` padding `14px 12px 32px`. Masthead aligns to start. Version text drops to 11px. `.table-wrap` max-height 470px. Toolbar wraps; every `label`/`.field` is full width and its select fills it; the view picker goes full width with equal buttons. |
| **780px** | The radio group fills the row with equal labels (`padding:8px 5px`, centred). View buttons get 40px height. Calculator form rows become one column. |
| **640px** | Masthead wraps; `.header-actions` becomes full width; header control height goes 34px -> 44px; the node matrix drops to one column; metric gap tightens to 10px; priority controls wrap. |
| **450px** | h1 -> `20px + offset`; the version block hides; the table drops to `11px + offset` with 5px cell padding and a 110px first column; panels `padding:11px`; toggles 10px; stat tiles 10px; the next-upgrade cost block stacks; dialog padding 12px. |
| **380px** | The radio labels may wrap (`white-space:normal`); the metric grid drops to one column. |

Plus two non-width queries:

```css
@media(pointer:coarse){
  .toolbar select,.header-controls button,#animations,.view-picker button,.world-picker span,
  .toggle,.stat-selector,.priority-controls button,.upgrade-cost button,.priority-expand,
  .save-actions button,.node-row input,.quiet-button,.resources input,.weekly-income select{min-height:44px}
}
@media(prefers-reduced-motion:reduce){
  .decoration-layer{display:none}
  .decoration-particle,.decoration-particle>i{animation:none}
}
```

Coarse pointers get 44px targets on real controls. The 34px density stays for rows; that is a reading surface, not a target.

**Size-critical controls must not hard-code a height.** An `id` selector beats a class list, so `#animations{min-height:34px}` silently outranks the `pointer:coarse` rule above and ships a 34px tap target on the most prominent header control. Size such controls from a custom property instead, so the media query can still raise them:

```css
#animations{height:var(--header-control-height,34px);min-height:var(--header-control-height,34px)}
```

The 640px breakpoint raises `--header-control-height` to 44px, and the coarse rule covers the rest.

**Table overflow is intentional.** `.table-wrap{overflow:auto}` with `white-space:nowrap` means the table scrolls inside its panel instead of breaking the page. Keep the 110px first column at <=450px so four numeric columns still fit.

## 4. Stacking order

`body{isolation:isolate}` creates one stacking context; everything else is a number inside it.

| z-index | Layer |
|---|---|
| 0 | `.decoration-layer` (background motes) |
| 1 | `.panel`, footer |
| 2 | `.decoration-front` (motes in front of panels, behind controls) |
| 3 | `.toolbar` |
| 4 | `.masthead` |
| 30 | `.load-status` toast |
| 50 | `.helper-panel` (also top layer when `popover`) |
| top layer | `<dialog>`, native popover, native select menus |

Implementation detail: `.shell` is `position:relative` but has no `z-index`, so its positioned children (panels, toolbar, masthead) participate in the body context rather than being trapped at one level. That is what lets the front decoration layer sit *between* panels and controls.

```css
.shell .panel{position:relative;z-index:1}
.shell .toolbar{position:relative;z-index:3}
.shell .masthead{position:relative;z-index:4}
```

## 5. Decoration layer (optional)

Motion is a separate, removable layer. The template ships **CSS-drawn motes** - no images, no requests. The reference site uses drifting petals and clouds from PNG sprites; that variant is below.

### 5.1 Shipped version: motes

```html
<div class="decoration-layer decoration-back" aria-hidden="true" hidden></div>
<div class="decoration-layer decoration-front" aria-hidden="true" hidden></div>
```

```css
.decoration-layer{position:fixed;inset:0;z-index:0;overflow:hidden;pointer-events:none;user-select:none;contain:strict}
.decoration-front{z-index:2}
.decoration-layer *{pointer-events:none}

.decoration-particle{position:absolute;display:block;width:var(--size,3px);height:var(--size,3px);opacity:0;
  animation:var(--duration,60s) linear var(--delay,0s) infinite mote-drift}
.decoration-particle>i{display:block;width:100%;height:100%;background:var(--ui-accent);
  opacity:var(--alpha,.12);border-radius:var(--radius,1px);filter:blur(var(--blur,0px));
  animation:particle-turn var(--turn-duration,9s) ease-in-out infinite alternate}

@keyframes mote-drift{
  0%{transform:translate3d(var(--start-x,0vw),-8vh,0);opacity:0}
  10%{opacity:1}
  55%{transform:translate3d(calc(var(--start-x,0vw) + var(--drift-x,0vw)),var(--mid-y,45vh),0)}
  90%{opacity:1}
  100%{transform:translate3d(var(--end-x,50vw),108vh,0);opacity:0}
}
@keyframes particle-turn{from{transform:rotate(-22deg)}to{transform:rotate(22deg)}}

.decoration-layer.is-paused .decoration-particle,
.decoration-layer.is-paused .decoration-particle>i{animation-play-state:paused}

@media(prefers-reduced-motion:reduce){
  .decoration-layer{display:none}
  .decoration-particle,.decoration-particle>i{animation:none}
}
```

Per-particle CSS variables (set from JS, never in a stylesheet):

| Variable | Meaning |
|---|---|
| `--size` | box size, px |
| `--duration`, `--delay` | full travel time and a **negative** delay so particles start mid-flight |
| `--alpha` | per-particle opacity |
| `--blur` | 0 or ~1px, fixed for the pass (never animated) |
| `--start-x`, `--end-x` | horizontal entry/exit, vw |
| `--drift-x`, `--mid-y` | mid-flight sway and height |
| `--turn-duration` | inner rotation cycle (7-14s, prime-ish so it never syncs) |
| `--radius` | `1px` for a flake, `50%` for dust |

Spawn code (the template's inline module, trimmed):

```js
function build() {
  const mobile = matchMedia('(max-width: 640px)').matches;
  const specs = [
    { layer: back,  count: mobile ? 7 : 16, min: 2, max: 5,  alpha: .14, blur: 0 },
    { layer: front, count: mobile ? 2 : 4,  min: 8, max: 14, alpha: .07, blur: 1 }
  ];
  for (const spec of specs) {
    spec.layer.replaceChildren();
    for (let i = 0; i < spec.count; i++) {
      const p = document.createElement('span');
      const duration = rand(38, 88);
      p.className = 'decoration-particle';
      p.style.cssText = `--size:${rand(spec.min, spec.max)}px;--duration:${duration}s;--delay:${-duration * Math.random()}s;` +
        `--alpha:${spec.alpha};--blur:${spec.blur}px;--start-x:${rand(2, 98)}vw;--end-x:${rand(-4, 104)}vw;` +
        `--drift-x:${rand(-8, 8)}vw;--mid-y:${rand(30, 65)}vh;--turn-duration:${rand(7, 14)}s;` +
        `--radius:${Math.random() < .3 ? '50%' : '1px'}`;
      p.append(document.createElement('i'));
      spec.layer.append(p);
    }
  }
}
```

Rules that matter:

- **Negative delay** - every particle is already somewhere on its path at first paint. No empty sky, no synchronised start.
- **Randomise duration, delay, start height and phase independently**, or the particles form a visible diagonal.
- **Build once per breakpoint**, not on every toggle, and `replaceChildren()` before rebuilding.
- **Never rebuild in an `animationiteration` handler** unless you also keep `duration`/`delay` untouched (see the petal variant) - resetting them makes the timeline jump.

### 5.2 Toggle, persistence, pause

```js
const KEY = 'hexa-ui-decorations';
const reduce = matchMedia('(prefers-reduced-motion: reduce)');
let enabled = true;
try { enabled = localStorage.getItem(KEY) !== 'off'; } catch {}

function sync() {
  const active = enabled && !reduce.matches;
  layers.forEach(layer => {
    layer.hidden = !active;
    layer.classList.toggle('is-paused', document.hidden);
  });
  control.disabled = reduce.matches;
  control.setAttribute('aria-pressed', String(active));
  control.textContent = `Animations: ${active ? 'On' : 'Off'}`;
  control.title = reduce.matches ? 'Disabled by your reduced motion setting' : 'Toggle decorative motion';
}
```

- `aria-pressed` reflects the real state; the label says "Animations: On/Off".
- `localStorage` is wrapped in `try/catch` (file://, private mode, blocked storage).
- Reduced motion **wins over** the stored preference: the button is disabled and explains why.
- `visibilitychange` adds `.is-paused`; CSS pauses every animation on the layer, including nested images. No timers, no frame loop.
- The toggle is a normal button in the header; it never moves when the layer is off.

### 5.3 Image variant: petals and clouds

If you have sprites, swap the `<i>` for an `<img>` and use these keyframes. Same layer, same contract.

```css
.decoration-particle{position:absolute;display:block;width:var(--size);opacity:0;
  animation-duration:var(--duration);animation-delay:var(--delay);
  animation-timing-function:linear;animation-iteration-count:infinite}
.decoration-particle img{display:block;width:100%;height:auto}
.decoration-particle.is-loaded{opacity:var(--alpha)}   /* add on img load */
.decoration-particle:not(.is-loaded){visibility:hidden}

.cloud{top:var(--top);left:0;animation-name:cloud-drift}
.petal{top:0;left:0;animation-name:petal-drift}
.petal img{filter:blur(var(--blur,0px));
  animation:petal-turn var(--sway-duration) var(--sway-delay) ease-in-out infinite alternate}
.petal-front{animation-name:foreground-drift}

@keyframes cloud-drift{
  from{transform:translate3d(calc(-1 * var(--size)),0,0)}
  to{transform:translate3d(100vw,0,0)}
}
@keyframes petal-drift{
  0%{transform:translate3d(var(--start-x),-15vh,0);opacity:0}
  10%{opacity:var(--alpha)}
  30%{transform:translate3d(var(--x1),var(--y1),0);opacity:var(--alpha)}
  60%{transform:translate3d(var(--x2),var(--y2),0);opacity:var(--alpha-low)}
  90%{opacity:var(--alpha)}
  100%{transform:translate3d(var(--end-x),115vh,0);opacity:0}
}
@keyframes petal-turn{
  from{transform:translateX(calc(-1 * var(--sway))) rotate(calc(var(--base-turn) - var(--turn)))}
  to{transform:translateX(var(--sway)) rotate(calc(var(--base-turn) + var(--turn)))}
}
/* Front petals are visible for part of a long cycle, then leave a quiet gap. */
@keyframes foreground-drift{
  0%,10%{transform:translate3d(var(--start-x),-15vh,0);opacity:0}
  15%{opacity:var(--alpha)}
  35%{transform:translate3d(var(--x1),var(--y1),0);opacity:var(--alpha-low)}
  55%{transform:translate3d(var(--x2),var(--y2),0);opacity:var(--alpha)}
  65%,100%{transform:translate3d(var(--end-x),115vh,0);opacity:0}
}
```

Petal variables: `--start-x`, `--x1`, `--x2`, `--end-x`, `--y1`, `--y2`, `--alpha`, `--alpha-low`, `--blur`, `--sway`, `--base-turn`, `--turn`, `--sway-duration`, `--sway-delay`. Cloud variables: `--top`, `--size`, `--duration`, `--delay`.

Re-roll the horizontal path only at the loop boundary, keeping `duration` and `delay`:

```js
wrapper.addEventListener('animationiteration', event => {
  if (event.target !== wrapper) return;
  if (!['petal-drift','foreground-drift'].includes(event.animationName)) return;
  if (!enabled || document.hidden || reduce.matches) return;
  randomisePass();   // sets --start-x/--end-x/--y1/--y2/--alpha/... only
});
```

Loop counts: background ~8 clouds at 120s + 35s variation, or ~36 petals at 32s + 18s; foreground 6 petals at 70-90s. Mobile halves the count. More than that stops reading as atmosphere and starts costing paint time.
