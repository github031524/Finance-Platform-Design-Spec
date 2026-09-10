# NC Futures — Platform Design Specification

**The master UI template.** One living document — no versions. It always describes the current system; when something changes, this document changes with it.

> One look for every app. This is the contract every NC Futures module follows. Each app keeps its own logic and data — but adopts the same shell, tokens, type, and component vocabulary defined here, so separate tools read as one platform. Load the one stylesheet, follow the five rules, and reuse the markup below verbatim.

> **Source of truth — this document wins.** If this spec and any app disagree, the spec is right and the app is out of date; bring the app up to the spec, never the reverse. That includes **Earnings Tracker** (`github031524/earnings-tracker`), which seeded this system and is still the best worked example of it, but is not the authority: the spec has moved ahead of it more than once, and an app lagging behind is a to-do, not a correction.
>
> Take `styles.css` (with its `fonts/` folder), `logo.svg` and `favicon.png` from **this repo** — they are the shipping artifacts, not illustrations; `sync-spec.sh` (§09, Step 0.5) fetches all of them in one command. Read the reference app's components (`Blueprint.tsx`, `PageTabs.tsx`, `NcFuturesLogo.tsx`) for how the markup goes together, then check what you copy against this document before shipping it.

> **No hub.** This is a redesign template applied to each app individually — there is no main dashboard, landing page, launcher, or hub of any kind, and none should be built. Every app is a fully standalone deploy, opened directly in its own browser tab. "Unified" means the apps *look* the same, not that they connect.

> **Full authorization — just do it.** The coding agent has full authority to carry out this conversion end to end without pausing for approval. Make every decision, run every step, commit as you go, and complete the whole job in one pass. Do not stop to ask permission or to confirm choices; only surface something if the app is genuinely broken and cannot proceed. The goal is to prompt once, walk away, and return to a finished conversion. **Exception:** if asked for *pick-your-changes mode* (§09a), that request wins — present the menu and wait, rather than converting straight through.

---

## 01 · The Five Rules

1. **Blueprint frames** — Every card, tile, panel and figure is a hairline-bordered line drawing with rounded corners (`--radius: 8px`). No drop shadows, and no solid fills except the primary button (below). Tints are fine — the light accent steps (100–300) and the `surface` grey are for hovers, row banding, tags and the tinted frame (`.blueprint--tint`) — as long as they stay tints.
2. **One color, plus one named exception** — Steel blue (`#5980a6`) is the only accent for everything except gain/loss. Gains and losses use a dedicated green/red pair (`#206f31` / `#b42d36`), independent of the accent ramp — a deliberate break from "one color," kept because red/green is a near-universal, safety-relevant trading convention and misreading it costs real money. Nothing else in the interface is colored.
3. **One typeface: Inter** — Inter for everything, all text and all numbers, no exceptions. Headings and figures use 600 weight uppercase; body is 400/500. Numbers are tabular.
4. **Visible grid** — Equal cells, hairline dividers, strong horizontal and vertical rhythm. Structure is drawn, not implied by whitespace alone.
5. **Data first** — No marketing copy inside the tool. Labels are short and uppercase; the numbers are the loudest thing on screen. Status/freshness text (e.g. "Updated 3:50 AM") lives inline near the control it describes, not in a separate breadcrumb strip.

**The two exceptions:** the primary button is the single solid object — an accent fill. Gain/loss color (see rule 2) is the other — a deliberate, permanent break from the one-color system, not a per-app choice.

---

## 02 · Foundations — Tokens

### Color

| Token | Value |
|---|---|
| `bg` | `#f2f2f3` |
| `surface` | `#e9e9ea` |
| `text` | `#1d1f20` |
| `accent` | `#5980a6` |
| `gain` | `#206f31` |
| `loss` | `#b42d36` |
| `hairline` | `#c9cacc` |

Gain/loss are chosen for legibility on small tabular numbers: both clear WCAG AA (4.5:1) on every background in the system — plain `bg`, banded `surface` rows, and `accent-200` hover — bottoming out at 4.84:1 on a hovered row. Re-check this pair against any new row background before adding one.

**Light only, and declared.** The stylesheet sets `color-scheme: only light` and every page carries `<meta name="color-scheme" content="only light">` in its `<head>` (§03). Without them, Chrome on Android's auto-dark mode recolours light pages by itself — inverting the one-colour system, gain/loss red and green included. **Native controls take the accent:** the stylesheet's `accent-color` makes checkboxes, radios and range sliders steel blue instead of the browser's default blue, so a bare checkbox is on-palette without a custom control.

**Accent ramp:**

| Step | Hex |
|---|---|
| 100 | `#edf2f7` |
| 200 | `#dbe4ee` |
| 300 | `#c0d0e0` |
| 400 | `#8fa9c4` |
| 500 (base) | `#5980a6` |
| 600 | `#4d6f90` |
| 700 | `#3f5b77` |
| 800 | `#32485e` |
| 900 | `#253544` |

What each step is for — this is the whole list; a new use picks from it rather than adding a shade:

| Step | Used for |
|---|---|
| 100 | tint fills: button hover, the current module in the switcher, tag fill, drop-zone drag-over, `.blueprint--tint` |
| 200 | row hover, pressed secondary button |
| 300 | scrollbar thumb, resize-handle tint |
| 400 | blank cells (`.nil`) — deliberately faint |
| 500 | the base: logo bars, tab underline, group-gap rule, tag border, focused input border, native checkbox/radio accent |
| 600 | primary button fill, placeholder text |
| 700 | primary button hover, focus ring |
| 800 | **all accent text** — labels, micro, table headers, tabs, field labels, tag text, tickers, plain links; the invalid-field border; primary button pressed |
| 900 | pressed secondary button text |

**800 is the one accent value for text.** Don't reach for 700 to make one kind of text "slightly different": 700 and 800 differ by only 1.34:1, so the distinction is invisible at body and label sizes while adding a second value to the ramp.

### Type — Inter only

- `--font-heading` = Inter · 600 · UPPERCASE — headings, figures, labels
- `--font-body` = Inter · 400/500 — paragraphs, table cells, and all numbers

One typeface carries all text and numbers; both `--font-heading` and `--font-body` are set to Inter. **Inter ships with the stylesheet:** the three faces (400 / 500 / 600) are self-hosted in `fonts/` next to `styles.css` — Latin and Latin Extended subsets, about 50KB each — and the stylesheet loads them itself. No Google Fonts link, no `@import`, no other font loader: that removed the only third-party request the apps made and a three-hop render-blocking chain, and it renders identically on every machine. Copy the folder wherever `styles.css` goes; the paths are relative to it. Inter has no Chinese glyphs, so Traditional-Chinese company names (Taiwan Screener) fall through to a *named* fallback — PingFang on Mac, JhengHei on Windows, Noto Sans TC elsewhere — rather than whatever each machine happens to pick; Latin text never reaches the fallbacks. Form controls inherit it too — the stylesheet resets `button`, `input`, `select` and `textarea` to the page font, so even a control that is missing its `.btn`/`.input` class renders in Inter rather than the browser's Arial (the classes still supply size, weight and case). Bare headings land on the scale by default — `h1` is the title size, `h2` the section head, `h3` the label — so a heading needs no class to look right; `.title` / `.section-head` / `.label` remain for other elements.

**Scale**

| Role | Size |
|---|---|
| Page title — a content entity (e.g. a ticker), **never the app name** | 34–40px |
| Section head | 15–16px, uppercase |
| KPI figure | 18px |
| Body | 13–15px |
| Label | 10.5px, uppercase, 0.13em tracking |
| Micro | 9–10px |

All numeric cells use `font-variant-numeric: tabular-nums`.

### Spacing & grid

- Space tokens `--space-1` … `--space-8` (3.4 → 27.2px, in 3.4px steps — 0.85 × a 4px base)
- **Radius is `8px` everywhere** — a deliberate, visible rounding. Not square.
- Content column max-width: `1680px`. Not full-bleed: unbounded width stretches table columns apart rather than adding useful density.
- Gutter: `clamp(16px, 3vw, 32px)`
- Content top/bottom padding: `clamp(16px, 3vw, 32px)`
- Card / tile grid gap: `clamp(12px, 1.5vw, 20px)`
- Table cell padding: `4px 10px` (`--table-cell-py` / `--table-cell-px`) — deliberately compact. Data density beats whitespace inside tables; this is the one place the 0.85× space scale is overridden.
- Control height: `28px` (`--control-h`) — buttons, inputs and selects are all exactly this tall, so a toolbar row lines up. Only `.btn-icon` may be smaller — never below 24 × 24px, the minimum touch target.

---

## 03 · The Shell — Chrome Every App Inherits

**Two** fixed layers wrap every module — not three. An app only ever supplies the content region; the top bar is identical across all apps.

**A · Global top bar** — 48px, sticky, hairline base. Brand mark on the left, the Modules switcher centered, an optional account chip on the right. **The brand mark is a link to the app's own start page** (`/`, or `#/` for a hash-routed app) — the near-universal way back from a deep view — and to nothing else: there is no hub to link to. **On phones** (under 640px) the switcher sits at the right of the bar instead of dead-centre — centred, it overlaps the brand mark below about 470px — and its menu opens centred under the bar. Nothing else changes, and the stylesheet handles it.

**The switcher is labelled with the current app**, not the word "Modules" — it reads as a "you are here" marker that happens to be clickable. Clicking it drops down the full module list (§04b); clicking an entry there opens that app **in a new tab**, leaving the current one in place. The current app is listed too, marked as active (`aria-current="page"`) and not a link.

The top bar carries only the brand mark and the Modules switcher — no global search. There is no cross-app directory or shared backend, so global chrome that implies one is deliberately left out. Any navigation between an app's own views lives in the content region as `.tabs` (§06); any search is local to that app's loaded data and built into its content region like any other component — never global chrome.

**There is no app context bar** — no breadcrumb or status strip as a second shell layer; don't build one. A page's own freshness/status readout (e.g. "Updated Jul 19, 3:50 AM", a live refresh indicator) lives inline in that page's own toolbar row instead — see §06 for the treatment.

**B · Content region** — the only part an app owns.

**Every page's `<head>`** carries the same seven lines and nothing that makes the app installable (§04a): the language, the charset, the viewport (without it a phone renders the page zoomed out), the light-only declaration (§02), the tab title, the favicon and the stylesheet.

**The tab title is `<Module name> · NC Futures`** — the module's registry name (§04b) first, so six open tabs stay tellable apart even when the browser truncates them; the brand second, for tab search. Never a page or entity name in it — the tab names the app, the content names itself.

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <meta name="color-scheme" content="only light" />  <!-- light-only; stops Android Chrome's auto-dark (§02) -->
  <title>Earnings Tracker · NC Futures</title>       <!-- module name first, brand second -->
  <link rel="icon" type="image/png" sizes="32x32" href="favicon.png" />  <!-- PNG, never SVG (§04a) -->
  <link rel="stylesheet" href="styles.css" />
  <!-- No <link rel="manifest">, no apple-touch-icon — either makes the app installable (§04a). -->
</head>

<header class="topbar">
  <!-- The brand mark links to THIS app's start page — "/" or "#/" for a
       hash-routed app — never to a hub (there is none). -->
  <a class="topbar__brand" href="/" aria-label="NC Futures — start page"><img src="logo.svg" alt="" /></a>
  <!-- Modules switcher — .topbar__modules keeps it absolutely centered in the
       top bar, dead-center regardless of the brand and account-chip widths on
       either side. The trigger is labelled with THIS app's name; the
       .blueprint--solid menu opens directly below it. -->
  <div class="topbar__modules">
    <button class="btn topbar__modules-trigger" type="button" aria-expanded="false" aria-controls="modules-menu">
      Earnings Tracker <span aria-hidden="true">▾</span>
    </button>

    <!-- A disclosure, not an application menu: a plain list of links (no
         role="menu"), so no arrow-key handling is owed. Toggle [hidden] to
         open and close — the script below does. Every entry opens in a new tab. -->
    <nav id="modules-menu" class="topbar__modules-menu blueprint blueprint--solid" aria-label="Modules" hidden>
      <span class="topbar__modules-item" aria-current="page">Earnings Tracker</span>
      <a class="topbar__modules-item" target="_blank" rel="noopener"
         href="https://options-analyzer-production-24d8.up.railway.app/">Options Analyzer</a>
      <!-- … one <a> per remaining module, in the §04b order … -->
    </nav>
  </div>
</header>

<main class="content">
  … APP CONTENT GOES HERE, INCLUDING THIS APP'S OWN TABS …
</main>
```

**The switcher works by keyboard and closes properly.** The button carries `aria-expanded` and `aria-controls`; Enter or Space opens it; **Escape closes it and returns focus to the button**; a click anywhere outside closes it; Tab moves through the entries like any list of links. It is a *disclosure*, not an application menu — no `role="menu"`/`menuitem`, which would promise arrow-key navigation nobody implements — and the ▾ is decorative (`aria-hidden`). The behaviour is nine lines; copy them as they are (or express the same in the app's framework):

```js
const trigger = document.querySelector(".topbar__modules-trigger");
const menu = document.getElementById("modules-menu");
const setOpen = (open) => { menu.hidden = !open; trigger.setAttribute("aria-expanded", String(open)); };
trigger.addEventListener("click", () => setOpen(menu.hidden));
document.addEventListener("click", (e) => {
  if (!menu.hidden && !e.target.closest(".topbar__modules")) setOpen(false);
});
document.addEventListener("keydown", (e) => {
  if (e.key === "Escape" && !menu.hidden) { setOpen(false); trigger.focus(); }
});
```

### Architecture

There is no shared console, no iframing, and no micro-frontend layer. Every app is a **fully standalone deploy** — it copies `styles.css` (with its `fonts/` folder), `logo.svg` and `favicon.png` into its own repo and reproduces the top bar locally, rather than mounting inside a parent shell. "Every app inherits the same chrome" means every app's markup matches, not that they run inside one host application.

There is no shared auth or shared data layer, implemented or implied — no SSO, no shared session store, no identity service. Every app gates itself, alone, with its own copy of the §10 access control and its own credential env vars. There is also no hub, launcher, or landing page tying the apps together — they are opened one at a time in separate browser tabs, and none should be created. Each app holds its own env-var API key and manages its own data independently.

---

## 04a · The Brand Mark

The logo is a **literal shared asset**, not a per-app redraw: three rising bars (accent-500 fill) beside the "NC Futures" wordmark, set in **Inter Regular at 90 units and outlined to paths**. Outlined on purpose: an SVG loaded as an `<img>` cannot load web fonts, so a live-text wordmark renders in whatever the viewer's machine happens to have — Arial on Mac and Windows, Roboto on Android — and never in Inter. As paths it is identical everywhere. Bar-to-text gap ≈ 20 units, a word space between the two words, on a 619×150 viewBox. Every app embeds the **exact same file**, unmodified — take `logo.svg` from **this repo** (the reference app's copy is the older live-text version and is out of date). Render it at `height: 34.56px` in the top bar; width follows automatically from the SVG's aspect ratio (≈ 143px).

Do not hand-recreate the mark as inline SVG shapes or a text lockup, and don't regenerate the wordmark from live text — use the file as-is.

**Favicon — required, and it must be the small PNG.** Every app serves the shared favicon so its browser tab carries the brand: the three bars alone, cropped square (the wordmark is illegible at 16px). Copy `favicon.png` from this repo unmodified — same rule as the logo, no per-app redraws — and declare it in `<head>`:

```html
<link rel="icon" type="image/png" sizes="32x32" href="favicon.png" />
```

An app whose tab shows the browser's default globe icon is missing its favicon and out of spec.

### Never make the app installable

These are browser tabs, not installed applications. Chrome offers **"Install app"** — rather than a plain shortcut — once a site looks installable, and the icon alone is enough to tip it over. Two rules keep every module on the same side of that line:

1. **The favicon is a 32×32 PNG. Never an SVG.** An SVG has no intrinsic size, so it satisfies the large-icon test at any size and Chrome starts offering to install the app. A 32×32 raster is unambiguously too small. This is the whole reason the file is a PNG — do not "upgrade" it back to SVG for sharpness.
2. **No web app manifest.** No `manifest.json`, no `<link rel="manifest">`. Nothing here needs one.

**A manifest cannot undo rule 1.** Adding a manifest with `display: browser` to suppress the prompt does not work — it was tried and changed nothing, because the manifest was never the cause. The icon is. Fix the icon.

**The top-bar logo stays SVG.** `logo.svg` is loaded as a picture in the page, not declared as the site icon, so it plays no part in this. Do not convert it to PNG — that only makes the brand mark blurry and fixes nothing.

**How to check.** In Chrome DevTools → Application → Manifest, a correctly built module reports `no-manifest` and `no-acceptable-icon`. Both are the desired state. The browser menu is the quick tell, and the wording differs by device:

| Device | Correct | Wrong |
|---|---|---|
| Desktop Chrome | "Create shortcut…" | "Install app" |
| Android Chrome | "Add to Home screen" | "Install app" |

"Install app" on either means the icon is an SVG or a manifest crept in. Check on a phone as well as a desktop — the Android prompt is the one people actually notice.

---

## 04b · The Module Registry

The canonical list of modules. **Every app hardcodes this same list, in this order**, in its own switcher — there is no shared backend or directory to fetch it from (§03 Architecture). It's a copied constant, like `styles.css` and `logo.svg`.

| Module | URL |
|---|---|
| Options Analyzer | `https://options-analyzer-production-24d8.up.railway.app/` |
| Earnings Tracker | `https://earnings-tracker-production-2c77.up.railway.app/#/` |
| Custom Indexer | `https://indexer-production-83a6.up.railway.app/#/` |
| Stock Screener | `https://parabolic-screener-production.up.railway.app/` |
| PEAD | `https://pead-watchlist-e1a53.up.railway.app/` |
| Taiwan Screener | `https://taiwan-revenue-screener-production.up.railway.app/#/` |

```js
// Copy verbatim into each app — this list is identical everywhere. Never edit it per app.
export const MODULES = [
  { name: "Options Analyzer", url: "https://options-analyzer-production-24d8.up.railway.app/" },
  { name: "Earnings Tracker", url: "https://earnings-tracker-production-2c77.up.railway.app/#/" },
  { name: "Custom Indexer",   url: "https://indexer-production-83a6.up.railway.app/#/" },
  { name: "Stock Screener",   url: "https://parabolic-screener-production.up.railway.app/" },
  { name: "PEAD",             url: "https://pead-watchlist-e1a53.up.railway.app/" },
  { name: "Taiwan Screener",  url: "https://taiwan-revenue-screener-production.up.railway.app/#/" },
];

// The ONE line that differs per app:
export const CURRENT_MODULE = "Earnings Tracker";
// render: m.name === CURRENT_MODULE ? inert <span aria-current="page"> : <a target="_blank" rel="noopener">
```

**Rules**

1. **Trigger label = the current app's name** — never the word "Modules".
2. **Entries open in a new tab** — `target="_blank" rel="noopener"`. The current tab never navigates away.
3. **The current app stays in the list**, rendered as inert text with `aria-current="page"` — not a link.
4. **Every change here — a module added *or* retired — is re-copied into every app** so all switchers stay identical (two modules have already been retired). A switcher missing an app, or still listing a retired one, is stale, not a variant. The current app is marked by the separate `CURRENT_MODULE` constant, never by editing the list.
5. Names here are the display names — use them verbatim, in the switcher label and in the tab title (`<Module name> · NC Futures`, §03).

---

## 05 · The Blueprint Frame

```html
<div class="blueprint">
  … content …
</div>
```

**No corner registration marks.** A `.blueprint` is a plain hairline-bordered box with a rounded radius (`--radius: 8px`) — nothing more. Don't draw `+` crosshairs at the corners.

No inline padding needed — `.blueprint` already carries a sensible default (`--space-4`, 13.6px). Add `style="padding:..."` only to override it for a specific tile. Default spacing below each panel is also `--space-4` — override for tiles that need tighter stacking. Inside a `.grid` a frame carries no outer margin: the grid's gap spaces the tiles and the grid carries the bottom margin, so a multi-row tile grid has even gaps.

**A frame wrapping a table has no padding** — directly, or through the table's `.table-scroll` wrapper (§08a) — the stylesheet zeroes it automatically, so the table sits flush against the border and its own cell padding does the spacing. Don't add `style="padding:0"` by hand. The same frame clips its rows to the rounded corners, so a hovered or banded last row never pokes square corners past the curve.

Use `.blueprint` on tiles, KPI cards, chart panels, filter asides, table wrappers, and floating panels (add `.blueprint--solid` for dropdowns/popovers that need an opaque fill so page content doesn't show through).

---

## 06 · Component Vocabulary

**Buttons & tags**

| Variant | Style | Class |
|---|---|---|
| Primary | accent fill | `.btn .btn-primary` |
| Secondary | neutral | `.btn` |
| Ghost | borderless; the hairline appears on hover | `.btn .btn-ghost` |
| Icon (compact) | icon-only, tight padding | `.btn .btn-icon` |
| Tag | accent | `.tag .tag-accent` |

```html
<button class="btn btn-primary" type="button">Run Scan</button>
<button class="btn" type="button">Open All</button>
<button class="btn btn-ghost" type="button">Clear</button>
<button class="btn btn-icon" type="button" aria-label="Remove"><svg …></svg></button>
<span class="tag tag-accent">ADR</span>
```

**Every control is the same height** — `--control-h`, 28px: buttons (whether `<button>` or `<a class="btn">`), `.input` text fields and selects all measure exactly that, so anything placed in one toolbar row lines up without per-app fixes. Button labels never wrap. `textarea.input` grows instead (at least two control heights); `.btn-icon` is the one control allowed to be smaller, down to a 24 × 24px minimum.

**KPI tile** — blueprint frame · label / figure / delta. The delta carries `.gain` or `.loss` (rule 2); a tile with nothing to compare against simply omits it. Tiles sit in a `.grid.grid--kpi` (auto-fit, 180px minimum per tile).

```html
<div class="grid grid--kpi">
  <div class="blueprint kpi">
    <div class="kpi__label">Revenue (TTM)</div>
    <div class="kpi__figure">$92.8B</div>
    <div class="kpi__delta gain">▲ +33.8% YoY</div>
  </div>
  <!-- … one .blueprint.kpi per figure … -->
</div>
```

**Data table** — `.table`, numbers right-aligned and tabular, compact row padding (`4px 10px`). **Every column after the first is right-aligned by default** — the numbers rule — so mark a text column that is not first (a company or name column) with `.text` on its `<th>` and `<td>`s to keep it left-aligned; `.num` forces the opposite. (The `.company` block sets its own alignment either way.) **Column headers are sticky**: rows scroll, the header row stays pinned so columns are always identifiable — see §08a for which `top` value to use, since it depends on whether the table scrolls with the page or inside its own box. Columns are **resizable** (§08a) and **sortable** (below) on every table.

| METRIC | Q3'25 | Q2'25 | YOY |
|---|---:|---:|---:|
| Revenue | $24.8B | $22.7B | +33.8% |
| Free cash flow | $8.2B | $4.4B | −4.2% |

**Empty cells** — a cell with no value carries an em-dash `—` marked `.nil`, in **any** column, not just names. It renders in accent-400: clearly present as "nothing here", but roughly 7× lighter than a real figure, so the eye skips it and lands on the numbers (rule 5). Don't render blanks at full text weight — a column of dashes then competes with the data beside it — and don't go lighter than `.nil` either, or they read as a rendering fault rather than a deliberate blank.

**Numbers & dates** — one way to write every figure, so six apps read as one. Rule 5 makes the numbers the loudest thing on screen; this is how they are spelled:

| What | Write it as | Not |
|---|---|---|
| A change (delta) | always signed — `+33.8%`, `−4.2%` — with a real minus sign (U+2212 `−`), never a hyphen | `33.8%`, `-4.2%` |
| Percentages | one decimal: `+1.2%` | `+1.23%`, `+1%` |
| Prices | two decimals: `228.10` | `228.1` |
| Large money | compact, one decimal: `$92.8B`, `$8.2M`, `$412K` | `$92,800,000,000` |
| Counts, shares, volume | thousands separated with commas: `12,480` | `12480` |
| Missing value | `—` marked `.nil` (Empty cells, below) | blank, `N/A`, `null` |
| Dates | `Jul 19`; add the year only when it isn't this year: `Jul 19, 2025` | `07/19/2025`, `2025-07-19` |
| Times | local time, 12-hour: `3:50 AM` | `15:50`, `03:50` |
| Status text | `Updated Jul 19, 3:50 AM` (Status/freshness text, below) | `Last refreshed at 15:50:12` |

Every delta carries `.gain` or `.loss` by its sign. The `▲`/`▼` glyph is standard in a KPI delta and optional in a table cell, where the sign and the colour already say it.

**Column-header tooltips** — every table column header carries a plain-language definition via the native HTML `title` attribute. **No custom tooltip component, no JavaScript, no CSS** — on hover (~1s browser delay) the browser renders its default tooltip: system font and size, positioned at the cursor. Styling is deliberately left to the browser/OS. The text states the metric's formula or meaning in one sentence:

```html
<th class="num" title="Trailing-twelve-month revenue, sum of the last four reported quarters">REVENUE (TTM)</th>
```

**Sortable columns** — every column showing a comparable value is sortable from its header, by mouse **and by keyboard**: the header text sits in a `<button class="th-sort">` inside the `<th>` (`.table th.sortable` — pointer cursor, no text selection). The button looks identical to a plain header, but Tab reaches it and Enter or Space sorts — a click handler on the `<th>` itself is unreachable without a mouse. The `title` tooltip (above) goes on the button; the resize handle (§08a) stays a sibling.

```html
<th class="num sortable" aria-sort="descending">
  <button class="th-sort" type="button" title="Last trade price">Price</button>  <!-- the ▼ is drawn from aria-sort -->
  <span class="th-resize"></span>
</th>
```

- **Click to sort, click again to flip.** First click on a numeric column sorts **descending** (biggest first — the finance default); on a text column **ascending** (A→Z). Set `aria-sort` (`ascending` / `descending`) on the active `<th>` and nothing else: the stylesheet draws the `▲`/`▼` from that attribute, inheriting the header colour (no new colours), so there is no glyph to paste into the text and keep in sync with what screen readers read. Inactive headers carry no `aria-sort` and so no glyph.
- **Sort by type** — numbers numerically, text case-insensitively, dates chronologically. Null/`—` cells always sort last, in either direction.
- **Composes with the rest of the table**: Open All opens tabs in the new order; group banding (§08b) recomputes from the new order; the §08a resize strip at the header's right edge is a drag target, not a sort target — a click there never sorts.
- Sorting is app code (state + re-render); the stylesheet supplies the affordance and the glyph.

**Symbol / ticker link** — every ticker symbol shown anywhere (table cells, KPI tiles, headers, detail asides) is a clickable `.symbol` link to its TradingView chart, opened in a new tab. Never render a bare, unlinked symbol. URL pattern: `https://www.tradingview.com/chart/3Ojf0qKU/?symbol=<SYMBOL>` — the shared chart layout `3Ojf0qKU` with the symbol appended (case-insensitive), e.g. `?symbol=aapl`. **Where a bare ticker is ambiguous, qualify it with the exchange** exactly as Open All does — `?symbol=TWSE:2330`, `?symbol=LSE:VOD` — through one shared helper, `tvSymbol(ticker, exchange)`, used by both the row's link and the Open All button, so the two can never disagree. A bare `2330` in a Taiwan Screener link resolves to the wrong chart while the button beside it opens the right one.

```html
<a class="symbol" href="https://www.tradingview.com/chart/3Ojf0qKU/?symbol=aapl"
   target="_blank" rel="noopener">AAPL</a>
```

**Plain links** — any link that isn't a ticker (a filing, a source, a docs page) is accent-800 with a thin underline. The stylesheet styles bare `<a>`, so no link is ever browser-blue or visited-purple; hover thickens the underline and nothing changes colour. Tickers keep `.symbol` (no underline at rest).

**Open All (bulk chart review)** — every view that lists stock tickers carries an **Open All** button (`.btn`, secondary — never the page's primary). One click opens each listed ticker's TradingView chart (the §06 symbol-link URL) in its own browser tab, replacing N clicks with one full-depth review session:

- **Order-aware** — tabs open in the list's current sort/filter state, so the on-screen ranking becomes the review order.
- **Symbol-aware** — each ticker maps to the exchange-qualified symbol where needed (e.g. `NASDAQ:AAPL`, `TWSE:2330`, `LSE:VOD`), so US, Asian and European listings all resolve to the right chart — through the same `tvSymbol()` helper the row's `.symbol` link uses (above), never a second mapping.
- **Stocks only** — derived rows (benchmarks, totals, index lines) are excluded.
- **Guardrail** — above ~25 tickers, a confirmation dialog states the tab count before opening.
- **Affordance** — the button's tooltip warns that the browser's pop-up blocker must allow the site, since blockers typically permit only the first tab.

**Company name cell** — long names in a company column are shortened by a `shortenCompanyName(name)` transform, truncated by CSS, then marquee-scrolled on hover:

1. **Strip a leading "The"** — `The Kraft Heinz Company` → `Kraft Heinz Company`.
2. **Strip suffixes** — repeatedly remove trailing corporate suffixes and share-class/ADR noise until nothing more matches: `Inc` / `Inc.`, `Corp` / `Corporation`, `Ltd`, `LLC` / `L.L.C.`, `plc`, `Holdings` / `Holding`, `Group`, `Technologies` / `Technology`, `& Co` / `Co.` / `Cos.`, `Class A` / `B` / `C` (any single-letter share class), `Series A`–`Z` `Preferred`, `Common Stock`, `American Depositary Shares` / `Receipts`, `ADR`, `ADS`, `Ordinary Shares (...)`, `Subordinate Voting Shares`. Loops until stable: `Foo Inc. Common Stock` → `Foo Inc.` → `Foo`.
3. **Cap to 3 words** — keep only the first 3 words that remain: `International Business Machines Corporation` → (strip `Corporation`) → `International Business Machines`.
4. **Truncate + marquee** — inside the company `<td>` (marked `.text`), a `.company` block wraps the name in a `.company__inner` span; anything too wide for the column gets a trailing `…`. On hover it marquee-scrolls at a steady, readable speed to reveal the full shortened name. Honors `prefers-reduced-motion` — the stylesheet switches the pan off and keeps the `…`, so reduced-motion users see the still, truncated name (the generic reset alone would leave a 0.01ms looping animation jittering).

   ✅ **The window measures itself.** `.company` is a CSS size container, so the pan always ends with the last character exactly at the cell's visible edge, and it stays right when a column is resized (§08a) — the app writes nothing. That is why `.company` goes on a `<div>` inside the `<td>`, not on the `<td>`: containment has no effect on table cells. (Legacy: an app that still puts `.company` on the `<td>` must write the cell's *inner* width — column width minus the two `--table-cell-px` paddings — into `--peek-window` on first render and on every resize. Writing the column width leaves the last 20px of the name unrevealed.)

   ♿ **The full name travels in `title` on the `<td>`** — the original, unshortened name, exactly as the data source gives it. The marquee is mouse-only: a phone has no hover, a keyboard has none, and a screen reader hears only the shortened three-word version. The `title` gives all three the full name (a long-press on a phone) with the same native-tooltip approach the column headers use, and no extra markup.

**Null case** — missing name → `shortenCompanyName` returns `null`; the cell renders an em-dash `—` using `.nil`, the same treatment as any empty cell (above), and carries no `title`.

```html
<td class="text" title="International Business Machines Corporation"><div class="company"><span class="company__inner">International Business Machines</span></div></td>
```

`.company` is the named instance of the general **peek-marquee pattern** (stylesheet §16), usable on *any* element whose text may overflow: a window (hidden overflow + `nowrap` + ellipsis) that is a CSS size container, an inner span that is **inline at rest** (the browser only draws the `…` for inline text — an `inline-block` child is silently clipped without one) and becomes `inline-block` only while panning, and on hover ellipsis→clip plus a `translateX` animation ending at `min(0px, calc(100cqw − 100%))` — `100cqw` is the window's own width, so the text slides left exactly until its last character reaches the window's right edge, and text that fits never moves. `6s linear infinite alternate` (tune `--marquee-speed`): a slow back-and-forth **pan**, not a looping ticker tape; layout never shifts, and mouse-out snaps back to the ellipsis. Reduced-motion users get the static ellipsis (stylesheet §16, reduced-motion block). The window needs no declared width: the container unit reads it live, so resized columns (§08a) stay exact. Give the element a width (a `<col>`, a `max-width`, a grid track) — the pattern never sizes itself.

**Toolbar** — `.toolbar`, the page's own control row: three zones on one grid — `.toolbar__left` (a selector, status text), `.toolbar__center` (the page's `.tabs`), `.toolbar__right` (action buttons). The middle stays exactly centred whatever sits either side and can never overlap it; under 720px the three zones stack into rows. Controls inside are content-sized and status text never wraps. Don't hand-build this with absolute positioning.

```html
<div class="toolbar">
  <div class="toolbar__left"><select class="input">…</select><span class="micro">12 symbols</span></div>
  <div class="toolbar__center"><div class="tabs">…</div></div>
  <div class="toolbar__right"><button class="btn">Open All</button><button class="btn btn-primary">Refresh</button></div>
</div>
```

**Tabs** — e.g. `WATCHLIST` · `UPCOMING`. Put them **in the toolbar's centre zone**, never in a dedicated row of their own. The active tab carries `aria-selected="true"`; that attribute is what the stylesheet styles, so keep it in step with the app's state. Tabs that are really links to routes (a hash-routed app's pages) are `<a class="tab" href="#/…">` with `aria-current="page"` on the active one instead — the stylesheet gives both the same look.

```html
<div class="tabs" role="tablist">
  <button class="tab" role="tab" aria-selected="true" type="button">Watchlist</button>
  <button class="tab" role="tab" aria-selected="false" type="button">Upcoming</button>
</div>
```

**Filter field** — `.field` wrapping a `<label>` and an `.input` (e.g. "Min YoY growth" → `+20%`). Link the two with `for`/`id` so clicking the label focuses the field and screen readers name it. `select.input` for a dropdown, `textarea.input` for multi-line.

```html
<div class="field">
  <label for="min-yoy">Min YoY growth</label>
  <input id="min-yoy" class="input" placeholder="+20%">
</div>
```

**Dropzone** — `.dropzone`, the input of the Upload / analyze recipe (§07): a frame the user drops a file or screenshot on, or clicks to browse. Three states, no new colours — idle is a *dashed* hairline (the one dashed border in the system, so a drop target reads as different from a content frame); drag-over adds `.is-dragover` (accent border, accent-100 tint) for the duration of the drag; once data is loaded, `.dropzone--slim` collapses it to a one-line "add another" bar. The label is `.micro`; the app handles the file input and the drag events.

```html
<div class="dropzone"><span class="micro">Drop a screenshot or click to browse</span></div>
<div class="dropzone dropzone--slim"><span class="micro">3 positions loaded</span><button class="btn btn-ghost">Add another</button></div>
```

**Status/freshness text** — plain `.micro` text (uppercase, 9.5px, accent-800), placed inline in a page's toolbar next to the action it describes (e.g. "Updated 3:50 AM" beside a "Refresh Data" button). **Not a pill** — no border, no fill. A badge around it implies something you can click or dismiss; this is a passive readout, and boxing it makes it compete with the actual controls in the same row.

```html
<span class="micro">Updated 3:50 AM</span> <button class="btn" type="button">Refresh Data</button>
```

`.tag` stays for things that really are tags — a short accent-marked classifier attached to a row or record, not a status line.

**States** — the moments a page has nothing to show get one look each, built from existing components, so six apps don't invent six spinners and six error boxes:

- **Loading** — disable the control and put a `.micro` readout beside it (`Loading…`), exactly like status text. No spinner.
- **Empty** — a `.blueprint.state` frame: one `.micro` line saying what would change it (`No rows match — widen the filters`) and, if there is one, the button that does (`.btn-ghost`).
- **Error** — the same frame: one `.micro` line naming the failure (`Couldn't load prices (HTTP 502)`) and a **Retry** `.btn`. Errors are not red — red is for losses (rule 2).
- **Invalid field** — `aria-invalid="true"` on the `.input` darkens its border to accent-800, and a `.micro` line under the field says how to fix it, linked with `aria-describedby`. No red here either.

```html
<button class="btn" disabled>Refresh Data</button> <span class="micro">Loading…</span>

<div class="blueprint state"><span class="micro">No rows match — widen the filters</span><button class="btn btn-ghost">Clear filters</button></div>

<div class="blueprint state"><span class="micro">Couldn't load prices (HTTP 502)</span><button class="btn">Retry</button></div>

<div class="field">
  <label for="min-yoy">Min YoY growth</label>
  <input id="min-yoy" class="input" aria-invalid="true" aria-describedby="min-yoy-err">
  <span id="min-yoy-err" class="micro">Enter a number, e.g. 20</span>
</div>
```

**Also in the stylesheet** — classes that exist but had no entry here until now:

- `.blueprint--tint` — a frame filled accent-100, e.g. today's cell in a tracker calendar.
- `.grid--week` — five equal columns (two on narrow screens), for a working-week calendar row.
- `.text` / `.num` — force a table column left (text) or right (numbers, tabular figures); see Data table above.
- `.figure` — a heading-weight tabular number outside a KPI tile; the same treatment as `.kpi__figure` without the tile.
- `[data-num]` — the numeric treatment of `.num` as an attribute, for generated markup.
- `.company--null` — identical to `.nil`; kept as a readable name at the company-cell call site.
- `.topbar__actions` + `.avatar` — the optional account chip at the right of the top bar: `<div class="topbar__actions"><span class="avatar">NC</span></div>`, a 28px hairline square with initials. Most modules omit it; the bar is complete without it.

---

## 07 · Layout Recipe Per App Type

Pick a recipe by **interaction pattern**, not by subject matter. A page that lists tickers isn't automatically Tracker — Tracker is specifically for date/calendar-grid data. A sortable watchlist or discovery table is Screener or List/Builder. If a page genuinely matches none of the five below, define a new named recipe rather than forcing the nearest one.

**No app-name page title.** The switcher in the top bar already names the current app (§03), so repeating it as a heading in the content region is redundant — don't render one. Every recipe below starts at its first working element (a toolbar, a filter aside, a dropzone), not at a title bar. A `.title` is only for a **content entity** the page is about — a ticker on a detail page, an index name on a builder page — never the app's own name.

**Dashboard / detail** *(a single ticker's full picture)*
Entity title (the ticker — not the app name) + price/status row → tabs → KPI tile row → chart panel + profile aside (2.1 : 1).

**Screener** *(e.g. Stock Screener, Taiwan Screener)*
Filter aside (260px, blueprint) + results table. Primary "Run / Scan" button in the aside.

**Tracker / calendar** *(e.g. Earnings Tracker — the reference app)*
`.toolbar` row (§06): left zone (e.g. a list/view selector) → centred `.tabs` → right zone (action buttons). A second `.toolbar` row below carries the primary input (e.g. "Add Symbols") plus a leading count/status readout (`.micro`, §06). Below that: the results table, wrapped in `.blueprint`, with row banding by date group.

**List / builder** *(e.g. Indexer)*
Master table (1.5) + detail aside (1) with a headline figure and holdings list. "New" primary button in the header.

**Upload / analyze** *(e.g. Options Position Analyzer)*
Status row → `.dropzone` (§06; `.dropzone--slim` once data is loaded) → KPI tile row → full-width chart panel → results table below. For tools where the input is a file/screenshot rather than a filter or ticker search.

---

## 08a · Resizable Table Columns

**Required on every data table.** Any column the user can read, they can widen — no table ships with fixed, immovable columns. Widths persist, so a table opens the way it was left:

- Column widths stored in component state, seeded from a `COLUMNS` config array, persisted to `localStorage` under an app-specific key.
- `<div class="table-scroll"><table class="table" style="table-layout:fixed; width:<sum>">` with a `<colgroup>` of `<col style="width:...px">` per column, driven by that state. The `.table-scroll` wrapper is the scroll box (below).
- A resize handle — `<span class="th-resize"></span>` as the last child of each resizable `<th>`: a `6px`-wide strip straddling the header's right edge, styled by the stylesheet. **Invisible at rest — no border, no vertical line.** The affordances are `cursor: col-resize` over the strip and an accent-300 tint that appears on hover and stays while dragging (add `.is-dragging` for the duration of the drag). Nothing is drawn when the column is not being resized.
- Drag updates width with pointer events: `pointerdown` on the handle calls `setPointerCapture`, then `pointermove`/`pointerup` on the handle itself — no `window` listeners; mouse, touch and pen all work, and a fast drag can't escape the strip — clamped to a `40px` minimum. The handle's `touch-action: none` keeps a finger drag from scrolling the page.

**Where the header sticks depends on where the table scrolls** — get this wrong and the header scrolls away. A sticky header pins to its nearest scrolling ancestor, so:

| The table… | Header pins with | Because |
|---|---|---|
| sits in a `.table-scroll` wrapper — **the usual case here** | `top: 0`, set by the wrapper class | it pins to that box, which already starts below the bar |
| scrolls with the page, no wrapper | `top: var(--topbar-h)`, the stylesheet default | it has to clear the 48px top bar |

Expect the first row. Fixed layout gives the table an explicit total width, which on a wide table overflows its frame — so wrap it: `<div class="blueprint"><div class="table-scroll"><table class="table">…`. The wrapper is the scroll box (add a `max-height` if it should scroll vertically too); the class pins the header at the box's own top and keeps the frame flush (§05), so there is nothing to override. A plain `overflow-x: auto` wrapper without the class does neither: the header floats 48px *down* inside the box with rows passing above it, and the frame keeps its padding. Never put `overflow: hidden` on a wrapper around a table — it silently kills sticky entirely.

**Resizing and the company cell:** nothing to do — the peek-marquee window (§06) measures itself, so a resized column pans correctly on the next hover. (Only a legacy `<td class="company">` needs its *inner* width written to `--peek-window` on every resize.)

---

## 08b · Row Banding & Grouping

For tables whose rows fall into natural groups (e.g. every ticker reporting on the same earnings date). All colors below are existing tokens — no new hues introduced for this pattern.

| Purpose | Value | Token |
|---|---|---|
| Default row background | `#f2f2f3` | `--color-bg` |
| Alternating band (every other group) | `#e9e9ea` | `--color-surface` |
| Row hover | `#dbe4ee` | `--color-accent-200` (`.table tbody tr:hover`, §08 — always wins over banding) |
| Standard row divider | `#c9cacc` (1px) | `--hairline-color` (`.table td` default, §08) |
| Group-gap divider | `#5980a6` (2px) | `--color-accent-500` |

**The rule, not just the colors:**

1. Assign each row's *group* (not each row) a stable index in the order groups first appear — e.g. group by earnings date, in the order rows are sorted.
2. Alternate background by that group index: even groups plain `bg`, odd groups `.row-band` (`--color-surface`). All rows within one group share the same background — the band marks the group, not the row.
3. Between the **last row of a group** and the first row of the next, check whether the groups are "adjacent" by whatever ordering the table uses (e.g. consecutive calendar days for a date-grouped table, consecutive ranks for a leaderboard). If they're adjacent, draw the normal 1px hairline. If there's a gap, apply `.row-groupgap` instead — the heavier 2px accent-colored rule — so the table visually breaks into clusters (e.g. "this week" vs "next week").
4. Hover always overrides banding — it outranks the banding class on specificity, so section order in the stylesheet doesn't matter — and is only ever active on the row under the cursor.

Utility classes for this are in `styles.css` §14 (`.row-band`, `.row-groupgap`) — apply them per-row from the page's own grouping logic; the stylesheet doesn't compute the grouping itself, since that's data-driven and differs per app (dates, ranks, categories, etc).

---

## 09 · Converting an Existing App

Converting means full replacement, not coexistence. Adopting this spec retires any prior theme, palette, or component library entirely — there is no compatibility mode that keeps old branding alongside the blueprint system. Proceed with the full replacement; the old look is being retired on purpose.

**Authorization:** the agent is fully authorized to complete every step below without stopping for approval. Where the prose says "raise it before starting" or "tell me which path you're taking," instead pick the sensible option, note the choice in your commit message, and keep going — do not block on it. This is overridden only by an explicit request for **pick-your-changes mode** (§09a).

### Step 0: inventory before touching anything

Before converting *or* rebuilding, have the coding agent read the current codebase — not recall the original prompt — and produce a written inventory: every route, every data flow, every business rule, every validation and edge case it can find. This inventory becomes the acceptance checklist for whichever path you take next.

**Retrofit vs. rebuild:** default to retrofitting incrementally — one step at a time, with a build/test and a git commit after each. Reach for a full ground-up rebuild only if the inventory pass shows the old theme is genuinely inseparable from the business logic throughout. Either way, check the result against the Step 0 inventory before calling it done — that's what actually prevents silent regressions.

### Step 0.5: refresh the shared files with one command

Copy `sync-spec.sh` from this repo into the app, next to where `styles.css` lives, and run it whenever the spec changes:

```sh
sh sync-spec.sh
```

It downloads the current `styles.css`, `fonts/`, `logo.svg` and `favicon.png` from this repo's `main` branch, so "bring the app up to the spec" starts with one command instead of four copy-pastes — copying by hand is exactly how apps fall behind. It refreshes files, not code: markup the spec asks for (the company cell, the toolbar, the switcher, sort headers) still needs reading the spec.

1. Give the page the §03 `<head>` — language, viewport, light-only, the `<Module name> · NC Futures` title, favicon (PNG not SVG), stylesheet — and delete any manifest; copy `styles.css`, its `fonts/` folder (kept next to it), `logo.svg` and `favicon.png` from this repo; wrap the app in the global top bar (brand mark + Modules switcher only — no context bar).
2. Swap every font to Inter — all text and numbers, no exceptions; uppercase every heading. Inter arrives with the stylesheet (self-hosted in `fonts/`, §02) — remove any Google Fonts link or other font loader the app had.
3. Recolor to tokens only — kill every stray hex, gradient and shadow. Use the hex values in §02.
4. Reframe every card/panel as `.blueprint` — hairline border, `8px` radius. **Do not add corner registration marks.**
5. Right-align numeric columns; set `tabular-nums`.
6. Gains → `#206f31`, losses → `#b42d36`. Nothing else colored.
7. Delete descriptions and sell copy; labels become short uppercase. Any status/freshness text moves inline into the relevant toolbar row (no context bar to put it in). Spell every number and date per §06 Numbers & dates — signed deltas with a real minus sign, one-decimal percentages, `Jul 19` dates.
8. Buttons → `.btn` (+ `.btn-primary` / `.btn-ghost` / `.btn-icon` as needed); inputs → `.input`; tables → `.table`. Page-local tabs → `.tabs`, in the centre zone of a `.toolbar` row per §06.
9. Wrap every ticker symbol in a `.symbol` link to its TradingView chart (`…/chart/3Ojf0qKU/?symbol=<SYMBOL>`, opened in a new tab, exchange-qualified where a bare ticker is ambiguous, via the same helper Open All uses) — no bare symbols anywhere.
10. Delete any heading that repeats the app's own name — the top-bar switcher already names it. Keep a `.title` only when it names a content entity (a ticker, an index).
11. Apply the **company name cell** treatment (§06) — shorten, truncate, marquee on hover, the full name in the cell's `title`, em-dash when missing.
12. Add an **Open All** button to every ticker-list view (§06).
13. Give every column header a native `title` tooltip (§06).
14. Make every comparable column sortable (§06) — header text in a `.th-sort` button so it works by keyboard — and every column resizable with persisted widths (§08a).
15. Give every view its **loading, empty and error states** and every validated field its invalid state (§06 States) — no app-specific spinners or red boxes.
16. Put the app behind the **§10 access gate**, and run the §10 secrets-hygiene check on the repo.

**Acceptance test:** put the converted app beside Earnings Tracker. If the top bar, type, frame treatment (no corners, `8px` radius) and number treatment are indistinguishable and only the content differs, it passes visually — but also re-check it against the Step 0 inventory to confirm nothing functional was lost along the way.

---

## 09a · Pick-Your-Changes Mode

An opt-in conversion: instead of converting straight through, the agent presents the proposed changes as a **clickable menu** and applies only the ones ticked. Use it when adopting the system gradually, or on an app where some of the old UI should survive.

**This mode overrides the "full authorization / just do it" clauses** in the header and §09. Do not edit, commit or push before the selection comes back.

**§10 access control is not a menu item.** It's a security requirement, not a look — apply it whether or not anything visual is ticked, and say so in the report. The only thing to ask about is if the app already has a working gate of its own.

### Protocol

1. **Inventory first (silently).** Run §09 Step 0 — read the codebase and inventory every route, data flow, business rule, validation and edge case. Keep it internal; don't print it. It stays the acceptance checklist.
2. **Present the menu.** Offer the applicable changes as selectable options via `AskUserQuestion`, grouped by area, **multi-select on**. The tool caps a round at 4 questions × 4 options, so run several rounds until every area is covered. Skip areas the app doesn't have — never show a "symbol links" option to an app with no tickers.
3. **Keep labels short** — see *Writing the options* below. This is the part that most often goes wrong.
4. **Apply only what's ticked.** Anything unticked is left exactly as-is, and is not raised again or "improved" in passing.
5. **Report** in one short block: what was applied, what was skipped by choice, and anything the selection makes inconsistent (see below).
6. Then commit, push, PR, merge as normal.

### Writing the options

The menu is for scanning, not reading. Hard limits:

- **Label: 5 words max**, in `old → new` form.
- **Description: one short line**, ~12 words max.
- **No jargon** — no CSS class names, no `§` references, no "blueprint", "token", "hairline" in the label.

| Too long | Right |
|---|---|
| Cards currently have drop shadows and rounded corners; they become hairline-bordered blueprint frames with an 8px radius | **Shadowed cards → hairline frames** |
| Replace the existing typeface with Inter for all text and numbers, with uppercase headings | **All fonts → Inter** |
| Wrap every ticker symbol in a link to its TradingView chart, opened in a new tab | **Symbols → TradingView links** |
| Remove the page heading that repeats the app's name since the switcher already shows it | **Drop app-name heading** |
| Convert tables to the platform table style with compact row padding and right-aligned numeric columns | **Tables → compact, numbers right** |

If a change can't be said in 5 words, it's two changes — split it.

### Suggested grouping

| Group | Changes offered |
|---|---|
| Shell | Top bar + brand mark · Modules switcher (§04b) · `<head>` + tab title (§03) · favicon (§04a) · remove app-name page title |
| Type | Inter everywhere · uppercase headings · tabular numbers |
| Color | Recolor to tokens · gain/loss pair · strip stray hex, gradients, shadows |
| Frames | Cards/panels → `.blueprint` hairline + `8px` radius |
| Table look | `.table` + compact density · right-aligned numerics · row banding · sticky headers |
| Table behavior | Sortable columns · resizable columns · header tooltips |
| Controls | Buttons → `.btn` · inputs → `.input` · page tabs → `.tabs` in a toolbar row · loading / empty / error states (§06) |
| Data cells | Symbols → TradingView links (§06) · company-name shortening + marquee (§06) · Open All on ticker lists (§06) |
| Copy | Delete sell copy · labels to short uppercase · status text inline |

### Dependencies

Some picks imply others — say so in the option text rather than silently pulling extras in:

- **Modules switcher** needs the **top bar**. Offer the bar first; grey the switcher out if the bar is declined.
- **Company-name marquee** needs the **company cell truncation** — it's one option, not two.
- **Row banding** and **resizable columns** both need the table converted to `.table` first.
- **Gain/loss colors** are part of the color group; picking "tokens only" without them leaves gains/losses uncolored — flag that.

A partial selection is a legitimate end state, not a half-finished job. But if the result is visibly inconsistent — new frames beside old shadowed cards, Inter beside the previous typeface — note it plainly in the report so the choice is informed. Do not fix it unasked.

---

## 10 · Access Control — Every App Is Private

Every module is a personal tool sitting on a public Railway URL. **No app ships without the gate below**, and it isn't optional in any mode: a new module isn't done, and a conversion isn't complete, until the gate is in place and verified.

The gate is deliberately the cheapest thing that works: **HTTP Basic Auth on the first request, a signed cookie for the next 30 days.** No login page, no user table, no session store, no identity provider, no new dependency — and no CSS, since there's nothing of ours to style.

### The rules

1. **Credentials come from the environment** — `APP_USERNAME` and `APP_PASSWORD`. Never hardcoded, never with a fallback default, never committed.
2. **Missing config fails closed.** If `APP_USERNAME`, `APP_PASSWORD` or `SECRET_KEY` is unset at startup, every route except `/health` returns `503` — a misconfigured app is unreachable, never open. The 503 body says nothing about *which* variable is missing; log that once at startup instead, by name only.
3. **Constant-time comparison** — `crypto.timingSafeEqual` / `hmac.compare_digest`, on equal-length inputs (hash both sides first, as below). Never `===` / `==` on a password.
4. **One prompt, then a cookie.** On success, set a signed, `HttpOnly`, `Secure`, `SameSite=Lax` cookie with a 30-day `Max-Age`, and accept it on later requests so a phone and a laptop each prompt once. **Stateless** — the signature *is* the proof. An in-memory session store would log you out on every deploy, since Railway replaces the container each time.
5. **Signed with `SECRET_KEY`** — HMAC-SHA256 over the payload, verified before the payload is read or trusted. A cookie that fails verification, or whose `exp` has passed, is treated as absent: fall through to the Basic Auth challenge.
6. **Everything is behind it** — pages, the SPA bundle and its assets, `/api/*`, the SPA catch-all route. Mount the middleware **once, above every route and above static-file serving**, so a new route is protected by default rather than by remembering to protect it.
7. **One public exception: `/health`** — Railway's healthcheck. It returns `{"status":"ok"}` and nothing else: no version, no env, no build info, no config state. Register it *above* the middleware; it is the only path allowed to skip.
8. **Never log credentials.** Not the password, not the `Authorization` header, not the cookie value — not in request loggers, not in error handlers that dump `req.headers`, not in debug output. Log the *outcome* (`401 GET /`), never the input.
9. **No custom login UI.** The browser's own Basic Auth dialog is the login screen — the same reasoning as the native column-header tooltips (§06): the OS draws it, there's nothing to style and nothing to keep in spec. Challenge with `WWW-Authenticate: Basic realm="NC Futures"`.
10. **Per app, not shared.** Each deploy carries its own variables. Reusing the same username/password across modules is fine and convenient, but each app still prompts once on its own domain — cookies don't cross domains, so opening a module from the switcher (§04b) prompts the first time and then goes quiet for 30 days. Rotating an app's `SECRET_KEY` invalidates its cookies immediately; that's the logout button.
11. **Four headers on every response, and no framework banner.** `Content-Security-Policy: frame-ancestors 'none'` — §03 forbids framing the apps; this is what enforces it — with `X-Frame-Options: DENY` for older browsers; `X-Content-Type-Options: nosniff`, so a browser never guesses a file type; `Referrer-Policy: no-referrer`, so a click out to TradingView doesn't hand it the app's URL. Turn off `X-Powered-By`. Set them once, above `/health`, so even the public route carries them. Nothing visible changes.

### Cookie format

```
ncf_auth = v1.<base64url(payload)>.<base64url(hmac_sha256(SECRET_KEY, payload))>
payload  = {"u":"<username>","exp":<unix-seconds>}
```

Verify by recomputing the HMAC over the received payload and comparing constant-time, then checking `exp` is in the future. The payload is signed, not encrypted — put nothing in it but the username and expiry.

### Reference implementation — Node / Express

The reference app's stack (Express serving the built Vite SPA). No new packages; the cookie is parsed by hand so `cookie-parser` isn't needed.

```js
// server/auth.js
import crypto from "node:crypto";

const { APP_USERNAME, APP_PASSWORD, SECRET_KEY } = process.env;
const MISSING = ["APP_USERNAME", "APP_PASSWORD", "SECRET_KEY"].filter((k) => !process.env[k]);
const COOKIE = "ncf_auth";
const MAX_AGE = 60 * 60 * 24 * 30; // 30 days

if (MISSING.length) console.error(`[auth] missing env: ${MISSING.join(", ")} — all routes will 503`); // names only, never values

const b64 = (v) => Buffer.from(v).toString("base64url");
const sign = (payload) => crypto.createHmac("sha256", SECRET_KEY).update(payload).digest();

// Hash both sides to a fixed width so the compare is constant-time AND
// never throws on a length mismatch — the length itself leaks nothing.
const safeEqual = (a, b) =>
  crypto.timingSafeEqual(
    crypto.createHash("sha256").update(String(a)).digest(),
    crypto.createHash("sha256").update(String(b)).digest(),
  );

function validCookie(token) {
  if (!token) return false;
  const [v, payload, sig] = token.split(".");
  if (v !== "v1" || !payload || !sig) return false;
  const expected = sign(payload);
  const got = Buffer.from(sig, "base64url");
  if (got.length !== expected.length || !crypto.timingSafeEqual(got, expected)) return false;
  try {
    const { exp } = JSON.parse(Buffer.from(payload, "base64url").toString());
    return typeof exp === "number" && exp > Math.floor(Date.now() / 1000);
  } catch {
    return false; // never trust an unverified payload
  }
}

function readCookie(req) {
  const hit = (req.headers.cookie || "")
    .split(";")
    .map((s) => s.trim())
    .find((s) => s.startsWith(`${COOKIE}=`));
  return hit ? decodeURIComponent(hit.slice(COOKIE.length + 1)) : null;
}

function issueCookie(res, user) {
  const payload = b64(JSON.stringify({ u: user, exp: Math.floor(Date.now() / 1000) + MAX_AGE }));
  res.cookie(COOKIE, `v1.${payload}.${b64(sign(payload))}`, {
    httpOnly: true, secure: true, sameSite: "lax", maxAge: MAX_AGE * 1000, path: "/",
  });
}

export function requireAuth(req, res, next) {
  if (MISSING.length) return res.status(503).type("text/plain").send("Server not configured");
  if (validCookie(readCookie(req))) return next();

  const header = req.headers.authorization || "";
  if (header.startsWith("Basic ")) {
    const [user, ...rest] = Buffer.from(header.slice(6), "base64").toString("utf8").split(":");
    if (safeEqual(user, APP_USERNAME) && safeEqual(rest.join(":"), APP_PASSWORD)) {
      issueCookie(res, user);
      return next();
    }
  }
  res.set("WWW-Authenticate", 'Basic realm="NC Futures", charset="UTF-8"');
  res.status(401).type("text/plain").send("Authentication required"); // never log `header`
}
```

```js
// server/index.js — order is the whole point
app.disable("x-powered-by");                                   // no framework banner
app.use((_req, res, next) => {                                 // rule 11: four headers on every response
  res.set({
    "Content-Security-Policy": "frame-ancestors 'none'",       // no framing — §03 forbids it, this enforces it
    "X-Frame-Options": "DENY",                                 // the same rule for older browsers
    "X-Content-Type-Options": "nosniff",                       // never guess a file type
    "Referrer-Policy": "no-referrer",                          // don't hand the app's URL to TradingView
  });
  next();
});
app.get("/health", (_req, res) => res.json({ status: "ok" })); // public, above the gate
app.use(requireAuth);                                          // ↓ everything below is private
app.use(express.static(distDir));
app.use("/api", apiRouter);
app.use((_req, res) => res.sendFile(path.join(distDir, "index.html"))); // SPA catch-all
```

**On the catch-all:** it's `app.use(...)` with no path, not `app.get("*", ...)`. Express 5 replaced its route parser and no longer accepts a bare `"*"` — it throws `PathError: Missing parameter name at index 1` at startup, so the app never listens. A pathless `app.use` is the one form that works identically on Express 4 and 5. (If you want a route rather than middleware, Express 5 spells it `app.get("/*splat", …)`, which Express 4 rejects — hence the middleware.)

### Reference implementation — Python / Flask

Same contract. `before_request` also covers Flask's `/static` route, which is the point.

```python
# auth.py
import base64, hashlib, hmac, json, os, time
from flask import Response, g, request

USER, PASSWORD, KEY = (os.environ.get(k) for k in ("APP_USERNAME", "APP_PASSWORD", "SECRET_KEY"))
MISSING = [k for k in ("APP_USERNAME", "APP_PASSWORD", "SECRET_KEY") if not os.environ.get(k)]
COOKIE, MAX_AGE = "ncf_auth", 60 * 60 * 24 * 30  # 30 days

_b64 = lambda b: base64.urlsafe_b64encode(b).decode().rstrip("=")
_unb64 = lambda s: base64.urlsafe_b64decode(s + "=" * (-len(s) % 4))
_sign = lambda payload: hmac.new(KEY.encode(), payload.encode(), hashlib.sha256).digest()
_digest = lambda v: hashlib.sha256((v or "").encode()).digest()  # fixed width, length-safe

def _valid_cookie(token):
    try:
        version, payload, sig = (token or "").split(".")
        if version != "v1" or not hmac.compare_digest(_unb64(sig), _sign(payload)):
            return False
        return json.loads(_unb64(payload))["exp"] > time.time()
    except Exception:
        return False

def require_auth():
    if request.path == "/health":
        return None                                    # the one public route
    if MISSING:
        return Response("Server not configured", 503)
    if _valid_cookie(request.cookies.get(COOKIE)):
        return None
    auth = request.authorization
    if auth and hmac.compare_digest(_digest(auth.username), _digest(USER)) \
            and hmac.compare_digest(_digest(auth.password), _digest(PASSWORD)):
        g.issue_cookie = True
        return None
    return Response("Authentication required", 401,
                    {"WWW-Authenticate": 'Basic realm="NC Futures", charset="UTF-8"'})

def issue_cookie(response):
    if g.pop("issue_cookie", False):
        payload = _b64(json.dumps({"u": USER, "exp": int(time.time()) + MAX_AGE}).encode())
        response.set_cookie(COOKIE, f"v1.{payload}.{_b64(_sign(payload))}",
                            max_age=MAX_AGE, httponly=True, secure=True, samesite="Lax", path="/")
    return response

# app.py
app.before_request(require_auth)
app.after_request(issue_cookie)

@app.after_request
def security_headers(response):                   # rule 11: four headers on every response
    response.headers.update({
        "Content-Security-Policy": "frame-ancestors 'none'",
        "X-Frame-Options": "DENY",
        "X-Content-Type-Options": "nosniff",
        "Referrer-Policy": "no-referrer",
    })
    return response
```

**FastAPI / Starlette:** same code as an `@app.middleware("http")` function, with the `/health` route exempted by path. **Anything else:** whatever the framework calls "middleware that runs before routing" — the rules above are the spec, the two listings are just the two stacks the platform actually runs.

### Railway setup

| Variable | Value |
|---|---|
| `APP_USERNAME` | your login name — short, not an email |
| `APP_PASSWORD` | a long random passphrase, generated not invented |
| `SECRET_KEY` | 64 hex chars from the command below — unrelated to the password |

Generate `SECRET_KEY` (any one):

```sh
openssl rand -hex 32
python3 -c "import secrets; print(secrets.token_hex(32))"
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

In the Railway dashboard: **project → service → Variables → New Variable** for each of the three; saving triggers a redeploy. Then **Settings → Deploy → Healthcheck Path = `/health`**. The dashboard is the only place these values live — never the repo, never a commit, never a screenshot in an issue. For local dev put the same three in a gitignored `.env`.

`Secure` cookies are fine on `http://localhost` (browsers treat localhost as a secure context), so the flag is unconditional — never weaken it behind a "dev mode" env check.

### Secrets hygiene — check on every build

1. **`.gitignore` covers `.env` and `.env.*`**, with `!.env.example` excepted. Commit a `.env.example` listing the variable *names* with empty values.
2. **Scan the working tree and the git history** for committed API keys, tokens and passwords — `git log -p -S<fragment>`, or a scanner like gitleaks/trufflehog across all refs.
3. **Report findings; do not rewrite history.** A force-pushed history rewrite is disruptive and doesn't help — anything already pushed must be assumed captured. **Rotating the exposed key at its provider is what actually neutralizes it**; do that first, then remove it from the working tree so the next commit is clean.

### Acceptance test

```sh
curl -sI https://APP/                      # 401 + WWW-Authenticate: Basic realm="NC Futures"
curl -si https://APP/health                # 200, body {"status":"ok"}  (-i, not -I: a HEAD request has no body)
curl -sI https://APP/api/anything          # 401 — API is not a side door
curl -sI https://APP/assets/index.js       # 401 — static assets are not a side door
curl -sI -u "$APP_USERNAME:$APP_PASSWORD" https://APP/
                                           # 200 + Set-Cookie: ncf_auth=v1…; Max-Age=2592000;
                                           #   Path=/; HttpOnly; Secure; SameSite=Lax
curl -sI -H 'Cookie: ncf_auth=v1.eyJ1IjoieCJ9.forged' https://APP/   # 401 — bad signature rejected
curl -sI https://APP/health | grep -i 'frame-ancestors'             # present — the headers ride on every response, the public one included
```

Then unset one variable in Railway and confirm the app returns `503` everywhere instead of letting anyone in. An app that answers `200` on any of the first four lines is out of spec and publicly readable.

Also confirm the app is **not installable** (§04a), on desktop and on a phone: desktop Chrome should offer "Create shortcut…" and Android Chrome "Add to Home screen" — never "Install app" on either — and DevTools → Application → Manifest should report `no-manifest` and `no-acceptable-icon`. If it offers to install, the favicon is an SVG or a manifest crept in.
