# Component recipes

Copy-paste blocks. Full CSS lives in `assets/template.css`; the values below are the ones that define the look. All controls inherit the root monospace face - never set `font-family` or a raw `font-size` on a component.

```html
<!-- Page frame, always in this order -->
<main class="shell">
  <header class="masthead">...</header>
  <div class="toolbar">...</div>
  <div class="workspace">...</div>
  <footer class="creator-footer">...</footer>
</main>
```

## 1. Masthead

```html
<header class="masthead">
  <div>
    <p class="kicker">Workspace / Local</p>
    <h1>Hexa Matrix Console</h1>
    <p class="subhead">Plan node upgrades by material cost and marginal gain.</p>
  </div>
  <div class="header-actions">
    <div class="header-controls">
      <button id="animations" type="button" aria-pressed="true">Animations: On</button>
    </div>
    <p class="version"><span>Snapshot 2026.10</span><br>Draft saved locally</p>
  </div>
</header>
```

```css
.masthead{display:flex;justify-content:space-between;gap:20px;align-items:end;border-bottom:1px solid var(--ui-panel-border);padding-bottom:15px}
.kicker{color:var(--ui-accent);letter-spacing:.13em;text-transform:uppercase;margin:0 0 8px;font-size:calc(11px + var(--ui-font-size-offset))}
h1{font-size:calc(25px + var(--ui-font-size-offset));letter-spacing:.01em;line-height:1.15;margin:0 0 7px}
.subhead{margin:0;color:#999}
.header-actions{display:grid;justify-items:end;gap:8px;align-self:start;margin-left:auto}
.header-controls{--header-control-height:34px;display:flex;align-items:center;justify-content:flex-end;gap:12px}
.header-controls button{height:var(--header-control-height);min-height:34px;white-space:nowrap}
```

`align-items:end` puts h1 on the rule; `align-self:start` on the actions keeps the controls at the top line. No `nav`, no logo image - the kicker is the eyebrow, h1 is the product name.

## 2. Toolbar

```html
<div class="toolbar">
  <label>Theme
    <select id="theme"><option value="">Teal (default)</option><option value="ren">Rose</option><option value="lynn">Green</option></select>
  </label>
  <div class="field">
    <span id="pool-label">Priority (Pool)</span>
    <div class="world-picker" role="radiogroup" aria-labelledby="pool-label">...</div>
  </div>
</div>
```

```css
.toolbar{display:flex;align-items:end;gap:14px;padding:16px 0}
.toolbar label,.field{display:grid;gap:5px;color:#aaa}
.toolbar select{min-width:145px;border-color:var(--ui-border)}
```

Use `<label>` wrapping for a single select; use `.field` + explicit `role="radiogroup"` for grouped controls. Labels are never floating or placeholder-only.

## 3. Panel and panel head

```html
<section class="panel" aria-labelledby="matrix-title">
  <div class="panel-head">
    <h2 id="matrix-title">Current Matrix</h2>
    <div class="save-actions">
      <button type="button">Undo</button><button type="button">Export</button><button type="button">Reset</button>
    </div>
  </div>
  ...
</section>
```

```css
.panel{position:relative;z-index:1;border:1px solid var(--ui-panel-border);background:var(--ui-panel);padding:13px 14px;min-width:0}
.panel-head{display:flex;justify-content:space-between;align-items:center;gap:12px;min-width:0}
.save-actions{display:flex;align-items:center;justify-content:flex-end;gap:6px;flex-wrap:wrap}
.save-actions button{min-height:32px}
```

Panels are flat: no radius, no shadow, no margin (the grid `gap` separates them). One `h2` per panel, `15px + offset`. Actions sit right, `32px` tall.

## 4. Completion track

```html
<div class="completion">
  <div class="completion-label"><span>Matrix completion</span><strong>68.4%</strong></div>
  <div class="completion-track" role="progressbar" aria-label="Matrix completion"
       aria-valuemin="0" aria-valuemax="100" aria-valuenow="68" aria-valuetext="68 percent complete">
    <span style="--value:68%"></span>
  </div>
</div>
```

```css
.completion{padding:13px 2px 11px}
.completion-label{display:flex;justify-content:space-between;align-items:baseline;gap:8px;color:var(--ui-soft)}
.completion-label strong{font-size:calc(16px + var(--ui-font-size-offset));color:var(--ui-bright)}
.completion-track{height:7px;margin-top:8px;border-radius:7px;background:var(--ui-active);overflow:hidden}
.completion-track span{display:block;height:100%;width:var(--value,0%);background:var(--ui-accent)}
```

Width is a CSS variable, not inline `width`, so it can be animated or driven from JS without touching styles. Keep `aria-valuetext` human-readable.

## 5. Metric grid

```html
<div class="totals">
  <div class="total"><small>Nodes complete</small><strong>34 / 48</strong></div>
  <div class="total"><small>Shards spent</small><strong>12,480</strong></div>
</div>
```

```css
.totals{display:grid;grid-template-columns:1fr;border-top:1px solid #444}
.summary-panel .totals{grid-template-columns:repeat(2,minmax(0,1fr));gap:12px;border:0}
.total{padding:10px 2px;border-bottom:1px solid #333;min-width:0}
.total small{display:block;color:#999;font-size:calc(10px + var(--ui-font-size-offset));text-transform:uppercase;letter-spacing:.04em}
.total strong{display:block;margin-top:5px;font-size:calc(14px + var(--ui-font-size-offset));font-variant-numeric:tabular-nums}
```

Two columns inside a summary panel, one column in a narrow sidebar. Labels are uppercase 10px; values are 14px tabular. Never make the value an accent colour - the accent budget is spent on progress and the next action.

## 6. Node matrix (grouped rows)

```html
<div class="node-list">
  <div class="node-column">
    <div class="node-group">
      <div class="group-label">Skill<span>3 nodes</span></div>
      <div class="node-row next">
        <span class="node-icon" aria-hidden="true">M4</span>
        <span class="node-label"><span class="node-name">Core Resonance</span><span class="skill-tag">M4</span></span>
        <input type="number" min="0" value="4" aria-label="Core Resonance level">
      </div>
    </div>
  </div>
</div>
```

```css
.node-list{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:14px;margin-top:4px}
.node-column{display:grid;align-content:start;gap:12px;min-width:0}
.group-label{display:flex;justify-content:space-between;gap:8px;color:#bcbcbc;padding:10px 4px 5px;border-bottom:1px solid #444;font-weight:700;font-size:calc(12px + var(--ui-font-size-offset))}
.group-label span{color:#858585;font-weight:400}
.node-row{display:grid;grid-template-columns:26px minmax(0,1fr) 52px;align-items:center;gap:8px;min-height:34px;border-bottom:1px solid #292929;border-left:2px solid var(--skill-accent);padding:3px 4px}
.node-row.next{background:color-mix(in srgb,var(--skill-accent) 17%,#1a1a1a)}
.node-row.next .node-name{font-weight:700}
.node-icon{display:grid;place-items:center;width:24px;height:24px;background:#303a32;color:#b9cfbd;font-size:calc(12px + var(--ui-font-size-offset));border:1px solid var(--skill-accent)}
.node-label{display:flex;align-items:center;gap:6px;min-width:0}
.node-name{min-width:0;line-height:1.3;overflow-wrap:anywhere}
.node-row input{width:52px;min-width:0;text-align:center;padding:3px 2px}
```

The 2px left spine in `--skill-accent` is the cheapest way to make a list feel "selected by category". The 52px numeric cell is fixed so every row aligns. The whole row is 34px; do not add a second line of metadata.

`minmax(0,1fr)` is mandatory: the name must be allowed to shrink and wrap, otherwise long labels blow out the grid.

## 7. Skill tag

```html
<span class="skill-tag">M3</span>
```

```css
.skill-tag{flex:none;display:inline-block;padding:1px 5px;border:1px solid #747d78;border-radius:3px;color:#dce4de;background:#303633;font-size:calc(10px + var(--ui-font-size-offset));font-weight:600;line-height:1.3;white-space:nowrap}
```

3px radius, 1px border, 10px text - the only place a 3px radius is allowed. Always decorative metadata, never a button.

## 8. Segmented stat bar

```html
<div class="stat-bar stat-main" aria-hidden="true">
  <i class="filled"></i><i class="filled"></i><i class="filled"></i><i class="filled"></i><i></i>
  <i></i><i></i><i></i><i></i><i></i>
</div>
```

```css
.stat-bar{display:grid;grid-template-columns:repeat(10,minmax(0,1fr));gap:2px;min-width:0;height:10px}
.stat-bar i{display:block;border:1px solid #3d4847;background:#202626}
.stat-bar.stat-main i.filled{background:var(--stat-main);border-color:var(--stat-main)}
.stat-bar.stat-additional i.filled{background:var(--stat-additional);border-color:var(--stat-additional)}
```

Ten cells, 2px gaps, no rounding. Always render all ten cells so the scale is readable; fill from the left. The parent carries the series class. When the bar is the only representation, expose the value as text (`aria-valuetext` on a `role="progressbar"` wrapper, or a visible `12 / 20`). In the template the bar is `aria-hidden` because the `12 / 20` summary is visible.

Mini variant inside a tile:

```css
.stat-mini-line{display:grid;grid-template-columns:minmax(0,1fr) 14px;align-items:center;gap:5px;font-size:calc(10px + var(--ui-font-size-offset));color:#aaa}
.stat-mini-line .stat-bar{height:5px;gap:1px}
.stat-mini-line .stat-bar i{border:0}
```

## 9. Stat selector tile

```html
<button type="button" class="stat-selector is-selected" aria-pressed="true">
  <span class="stat-selector-name">Power</span>
  <span class="stat-mini-preview" aria-hidden="true">...</span>
  <span class="stat-selector-summary">12 / 20</span>
</button>
```

```css
.stat-selectors{display:grid;grid-template-columns:repeat(3,minmax(0,1fr));gap:8px;margin-top:10px}
.stat-selector{display:flex;flex-direction:column;align-items:center;gap:4px;width:100%;min-width:0;min-height:113px;padding:8px 5px;border:1px solid var(--ui-panel-border);background:#1b1b1b;text-align:center}
.stat-selector.is-selected{border-color:var(--ui-accent);background:var(--ui-active)}
```

A tile is a real `<button>` with `aria-pressed`; selection is a border + `--ui-active` fill, never a coloured card. Keep the preview bars `aria-hidden` and repeat the number as text.

## 10. Priority table

```html
<div class="table-wrap" tabindex="0" role="region" aria-label="Upgrade priority list">
  <table class="priority-table">
    <thead><tr><th scope="col">Node</th><th scope="col">Target</th><th scope="col">Shards</th><th scope="col">Cores</th><th scope="col">Gain</th></tr></thead>
    <tbody>
      <tr class="next"><td><span class="skill-cell"><span class="dot" aria-hidden="true"></span>Core Resonance</span></td><td>5</td><td>1,240</td><td>3</td><td>+2.30%</td></tr>
      <tr class="done"><td>...</td><td>5</td><td>0</td><td>0</td><td class="zero">+0.00%</td></tr>
    </tbody>
  </table>
</div>
```

```css
.table-wrap{overflow:auto;max-height:650px;margin-top:12px;border:1px solid #383838}
.priority-table{width:100%;border-collapse:collapse;white-space:nowrap;font-variant-numeric:tabular-nums;font-size:calc(12px + var(--ui-font-size-offset))}
.priority-table th{position:sticky;top:0;z-index:1;background:#222;color:#aaa;text-align:left;font-weight:400;border-bottom:1px solid #555;padding:7px 8px}
.priority-table td{padding:5px 8px;border-bottom:1px solid #292929;height:29px}
.priority-table td:nth-child(n+2),.priority-table th:nth-child(n+2){text-align:right}
.priority-table td:first-child{min-width:150px}
.priority-table tr:nth-child(even){background:#1a1a1a}
.priority-table tr.next{outline:2px solid var(--skill-accent);outline-offset:-2px;background:color-mix(in srgb,var(--skill-accent) 17%,#1a1a1a)}
.priority-table tr.done{opacity:.45}
.priority-table.hide-done tr.done{display:none}
.priority-table .skill-cell{display:flex;gap:8px;align-items:center;min-width:0;white-space:normal;line-height:1.25}
.priority-table .skill-cell .dot{width:5px;height:18px;flex:none;background:var(--skill-accent)}
.priority-table td:last-child{color:var(--ui-accent);min-width:95px}
```

Rules that make it feel like a terminal:

- `border-collapse:collapse`, 29px rows, sticky head with an **opaque** background (`#222` - never translucent, rows would show through).
- Every column except the first is right-aligned; numerals are tabular.
- The next row is a `color-mix` fill plus an inset 2px outline, not a border (borders would shift the layout).
- Completed rows are `opacity:.45`; `.hide-done` on the table removes them.
- The scroll container is focusable (`tabindex="0"`) with `role="region"` and a label, so keyboard users can scroll it.
- Row height stays fixed even with a non-wrapping first cell; put long names in `.skill-cell` which is allowed to wrap.

## 11. Next-upgrade card

```html
<div class="next-upgrade">
  <p class="upgrade-kicker">Next upgrade</p>
  <div class="upgrade-heading">
    <span class="upgrade-label"><span class="node-icon" aria-hidden="true">M4</span><strong>Core Resonance</strong></span>
    <span class="fd-gain">+2.30% FD</span>
  </div>
  <div class="upgrade-cost">
    <div class="upgrade-cost-details">Level 4 &rarr; 5 &middot; 1,240 shards &middot; 3 cores</div>
    <button type="button" data-dialog="cost-dialog">Add level</button>
  </div>
</div>
```

```css
.next-upgrade{margin-top:12px;border:1px solid var(--skill-accent);background:color-mix(in srgb,var(--skill-accent) 12%,#161616);padding:10px 12px}
.upgrade-heading{display:grid;grid-template-columns:minmax(0,1fr) auto;align-items:center;gap:8px;min-width:0;margin:5px 0}
.upgrade-label{display:flex;align-items:center;gap:8px;min-width:0}
.upgrade-heading strong{min-width:0;overflow-wrap:anywhere}
.fd-gain{color:var(--ui-accent);font-size:calc(11px + var(--ui-font-size-offset));font-variant-numeric:tabular-nums;white-space:nowrap}
.upgrade-heading .fd-gain{font-size:calc(13px + var(--ui-font-size-offset));font-weight:700}
.upgrade-cost{display:flex;align-items:center;justify-content:space-between;gap:12px;padding:6px 0 0;border-top:1px solid #444}
.upgrade-cost button{flex:none;min-height:32px;border-color:var(--skill-accent);background:#252525;white-space:nowrap}
.upgrade-cost button:hover{background:color-mix(in srgb,var(--skill-accent) 22%,#252525)}
```

The 1px solid highlight border + 12% fill is the strongest emphasis the system allows. One card per view. Button hover is the only 22% mix.

## 12. Radio segmented control

```html
<div class="field">
  <span id="pool-label">Priority (Pool)</span>
  <div class="world-picker" role="radiogroup" aria-labelledby="pool-label">
    <label><input type="radio" name="pool" value="shards" checked><span>Shards (Weekly)</span></label>
    <label><input type="radio" name="pool" value="cores"><span>Cores (Daily)</span></label>
  </div>
</div>
```

```css
.world-picker{display:flex;gap:2px;min-width:0}
.world-picker label{display:block;cursor:pointer;min-width:0}
.world-picker input{position:absolute;opacity:0;width:1px;height:1px}
.world-picker span{display:block;padding:5px 10px;border:1px solid #444;background:#1d1d1d;color:#aaa;white-space:nowrap}
.world-picker input:checked+span{background:var(--ui-active);border-color:var(--ui-accent);color:var(--ui-bright)}
.world-picker input:focus-visible+span{outline:1px solid var(--ui-accent);outline-offset:2px}
```

Real radios, visually hidden with `opacity:0` + 1px size (never `display:none` - that kills the keyboard). Focus is drawn on the adjacent `span`. Wrap the group in a labelled container rather than fighting `fieldset`/`legend` flex layout.

## 13. Button group (view switch)

```html
<div class="view-picker" role="group" aria-label="View">
  <button type="button" data-view="matrix" aria-pressed="true">Matrix</button>
  <button type="button" data-view="priority" aria-pressed="false">Priority</button>
  <button type="button" data-view="cost" aria-pressed="false">Cost</button>
</div>
```

```css
.view-picker{display:flex;margin-left:auto;gap:2px;flex-shrink:0;min-width:0}
.view-picker button{min-height:32px;color:#aaa}
.view-picker button[aria-pressed="true"]{background:var(--ui-active);border-color:var(--ui-accent);color:var(--ui-bright)}
```

`aria-pressed` is the selected state; no `.active` class. 2px gaps, no rounding. If the group changes a view, put `data-view` on a container and drive CSS from it (see `layout-and-motion.md`).

## 14. Toggle

```html
<label class="toggle"><input type="checkbox" checked> Include support nodes</label>
```

```css
.toggle{display:flex;align-items:center;gap:5px;white-space:nowrap;font-size:calc(11px + var(--ui-font-size-offset));color:#aaa}
input[type="checkbox"]{margin:0;accent-color:var(--ui-accent);width:20px;height:20px;flex:none}
.toggle input[type="checkbox"]{cursor:pointer}
```

Native checkbox, accent-coloured. No custom switch - it would need its own focus, disabled and RTL handling for no gain.

The box is 20px, not the browser default 13px: on a coarse pointer a 13px box is a poor tap target. The 44px requirement is met by the `.toggle` label row around it, not by the box.

## 15. Save toast

```html
<div class="load-status" role="status" data-status="saved">
  <span class="status-dot" aria-hidden="true"></span>Saved locally &middot; just now
</div>
```

```css
.load-status{position:fixed;bottom:12px;left:50%;transform:translateX(-50%);z-index:30;display:flex;align-items:center;gap:8px;width:max-content;max-width:calc(100% - 24px);padding:8px 12px;border:1px solid var(--ui-panel-border);background:var(--ui-page);box-shadow:0 3px 12px #0008;font-size:calc(12px + var(--ui-font-size-offset))}
.status-dot{width:7px;height:7px;flex:none;border-radius:50%;background:#58c98c}
.load-status[data-status="saving"] .status-dot{background:#e8bf63}
.load-status[data-status="error"] .status-dot{background:#e77979}
```

Show it only while there is something to say; hide it with `hidden` otherwise (the template ships it hidden so it never covers content). `role="status"` announces the text change - change the text, not just the dot colour.

## 16. Modal dialog

```html
<dialog id="cost-dialog" aria-labelledby="cost-title">
  <div class="dialog-head">
    <h2 id="cost-title">Upgrade cost</h2>
    <form method="dialog"><button type="submit">Close</button></form>
  </div>
  <div class="dialog-body">
    <p><strong>Core Resonance</strong> &middot; Level 4 &rarr; 5</p>
    <div class="dialog-actions">
      <form method="dialog"><button type="submit">Cancel</button></form>
      <form method="dialog"><button type="submit" class="accent-button">Apply</button></form>
    </div>
  </div>
</dialog>
```

```css
dialog{width:calc(100% - 40px);max-width:560px;max-height:calc(100dvh - 40px);margin:auto;padding:0;overflow:auto;border:1px solid var(--ui-accent);border-radius:0;background:var(--ui-panel);color:#ddd}
dialog::backdrop{background:rgba(0,0,0,.75)}
.dialog-head{position:sticky;top:0;z-index:1;display:flex;align-items:center;justify-content:space-between;gap:12px;padding:16px 20px;background:var(--ui-panel);border-bottom:1px solid #555}
```

Native `<dialog>`: top layer, focus trap, Escape and `::backdrop` for free. Open with `showModal()`. Square corners, accent 1px border, sticky head. `form method="dialog"` closes and returns the value - no JS close handler needed.

## 17. Floating helper panel

```html
<button type="button" popovertarget="tracks-helper" aria-label="About stat tracks">?</button>
<div id="tracks-helper" popover class="helper-panel">
  <h3>Stat tracks</h3>
  <p>Each track holds 20 points; one segmented cell is one point.</p>
</div>
```

```css
.helper-panel{position:fixed;inset:auto 16px 64px auto;margin:0;z-index:50;width:min(380px,calc(100vw - 32px));max-height:calc(100dvh - 96px);overflow:auto;padding:14px 16px;border:1px solid var(--ui-accent);border-radius:2px;background:#19191d;color:#d9d9d9;box-shadow:0 8px 24px #0009;font-size:calc(12px + var(--ui-font-size-offset));line-height:1.5}
```

Use the native `popover` attribute: light-dismiss, Escape and top layer are handled. Pin it with `inset` so it does not jump with the anchor. If you must use `role="tooltip"`, it cannot contain interactive content - this panel can.

## 18. Footer

```html
<footer class="creator-footer">
  <span class="creator-credit">Hexa UI</span>
  <span class="quiet">Plain HTML, CSS and one inline module</span>
</footer>
```

```css
.creator-footer{display:flex;justify-content:center;align-items:center;flex-wrap:wrap;gap:12px;margin-top:20px;padding-top:16px;border-top:1px solid var(--ui-panel-border);font-size:calc(12px + var(--ui-font-size-offset))}
.creator-credit{color:var(--ui-accent)}
```

One accent word, the rest quiet. No badges, no external images.

## Accessibility checklist per component

| Component | Required |
|---|---|
| Toolbar select | `<label>` wrapping the control, or `for`/`id` |
| Radio group | `role="radiogroup"` + `aria-labelledby`, real radios |
| Button group | `role="group"` + `aria-label`, `aria-pressed` on each button |
| Stat tile | `<button>` + `aria-pressed`, value repeated as text |
| Progress | `role="progressbar"` + `aria-valuemin/max/now` + `aria-valuetext` |
| Bar | `aria-hidden` when a text value is present |
| Table | `<th scope="col">`, focusable scroll wrapper with `role="region"` and a label |
| Toggle | real checkbox inside a `<label>` |
| Toast | `role="status"` |
| Dialog | `aria-labelledby`, `showModal()`, `form method="dialog"` |
| Icon | `aria-hidden="true"`; text glyphs are not an icon font |
| Focus | never remove the 1px accent outline |
