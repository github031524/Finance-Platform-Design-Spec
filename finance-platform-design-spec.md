# NC Futures — Platform Design Specification

**Version 1.0 · Industry System**

> One look for every app. This is the contract every NC Futures module follows. Each app keeps its own logic and data — but adopts the same shell, tokens, type, and component vocabulary defined here, so five separate tools read as one platform. Load the one stylesheet, follow the five rules, and reuse the markup below verbatim.

---

## 01 · The Five Rules

1. **Blueprint frames** — Every card, tile, panel and figure is a square, hairline-bordered line drawing with `+` corner marks. No rounded corners, no filled surfaces, no drop shadows as decoration.
2. **One color, plus one named exception** — Steel blue is the only accent for everything except gain/loss. Gains and losses use a dedicated green/red pair (`#2e8b57` / `#c0392b`), independent of the accent ramp — a deliberate break from "one color," kept because red/green is a near-universal, safety-relevant trading convention and misreading it costs real money. Nothing else in the interface is colored.
3. **One typeface: Inter** — Inter for everything, all text and all numbers, no exceptions. Headings and figures use 600 weight uppercase; body is 400/500. Numbers are tabular.
4. **Visible grid** — Equal cells, hairline dividers, strong horizontal and vertical rhythm. Structure is drawn, not implied by whitespace alone.
5. **Data first** — No marketing copy inside the tool. Labels are short and uppercase; the numbers are the loudest thing on screen.

**The two exceptions:** the primary button is the single solid object — an accent fill that still keeps square corners and corner marks. Gain/loss color (see rule 2) is the other — a deliberate, permanent break from the one-color system, not a per-app choice.

---

## 02 · Foundations — Tokens

### Color

| Token | Value |
|---|---|
| `bg` | `#f2f2f3` |
| `surface` | `#e9e9ea` |
| `text` | `#1d1f20` |
| `accent` | `#5980a6` |
| `gain` | `#2e8b57` |
| `loss` | `#c0392b` |

**Accent ramp:** `--color-accent-100` … `--color-accent-900`. Light steps for tinted fills and hovers; 500 is base; 700–900 for text on tint and pressed states.

### Type — Inter only

- `--font-heading` = Inter · 600 · UPPERCASE — headings, figures, labels
- `--font-body` = Inter · 400/500 — paragraphs, table cells, and all numbers

One typeface carries all text and numbers; both `--font-heading` and `--font-body` are set to Inter.

**Scale**

| Role | Size |
|---|---|
| Page / app title | 34–40px |
| Section head | 15–16px, uppercase |
| KPI figure | 18px (was 27px — dropped after real KPI rows read as bulky; override up for a single hero figure) |
| Body | 13–15px |
| Label | 10.5px, uppercase, 0.13em tracking |
| Micro | 9–10px |

All numeric cells use `font-variant-numeric: tabular-nums`.

### Spacing & grid

- Space tokens `--space-1` … `--space-8` (3.4 → 27px, a 0.85× scale)
- Radius is `0` everywhere (square)
- Content column max-width: `1680px` — raised from an earlier 1320px once real apps showed too much empty margin on normal-width monitors. Not full-bleed: unbounded width stretches table columns apart rather than adding useful density.
- Gutter: `clamp(16px, 3vw, 32px)`
- Content top/bottom padding: `clamp(16px, 3vw, 32px)` (tightened from `clamp(22px, 3vw, 40px)`)
- Card / tile grid gap: `clamp(12px, 1.5vw, 20px)`
- Table cell padding: `5px 10px` (`--table-cell-py` / `--table-cell-px`) — deliberately compact. Data density beats whitespace inside tables; this is the one place the 0.85× space scale is overridden.

---

## 03 · The Shell — Chrome Every App Inherits

Three fixed layers wrap every module. An app only ever supplies the content region; the top bar and context bar are identical across all apps.

**A · Global top bar** — 62px, sticky, hairline base.
`NC FUTURES ▦ · Modules ▾ · Search any ticker… ◌ · NC`

**B · App context bar** — breadcrumb + status, surface fill.
`Workspace / Module name` · `[status tag]`

**C · Content region** — the only part an app owns.

```html
<header>…global top bar…</header>

<div> <!-- app context bar -->
  Workspace / [App name] [status tag]
</div>

<main style="max-width:1680px; margin:0 auto;
      padding:clamp(16px,3vw,32px) clamp(16px,3vw,32px);">
  … APP CONTENT GOES HERE …
</main>
```

### Architecture

There is no shared console, no iframing, and no micro-frontend layer. Every app is a **fully standalone deploy** — it copies `styles.css` into its own repo and reproduces the three shell layers above locally, rather than mounting inside a parent shell. "Every app inherits the same chrome" means every app's markup matches, not that they run inside one host application.

There is no shared auth or shared data layer, implemented or implied. The `[status tag]` in the context bar is just mockup copy for whatever a given app wants to show there (e.g. a data-freshness or connection indicator) — not a real login or session requirement. Each app holds its own env-var API key and manages its own data independently.

---

## 04 · The Blueprint Frame — The Signature

```html
<div class="blueprint">
  <i class="corner tl"></i><i class="corner tr"></i>
  <i class="corner bl"></i><i class="corner br"></i>
  … content …
</div>
```

No inline padding needed — `.blueprint` already carries a sensible default. Add `style="padding:..."` only to override it for a specific tile.

The `.blueprint` class draws the square hairline border; the four `<i class="corner">` children draw the registration marks. Default inner padding is `--space-4` (13.6px), and default spacing below each panel is also `--space-4` — override either with inline styles on tiles that need more room or tighter stacking. **Never drop a corner.** Use it on tiles, KPI cards, chart panels, filter asides and table wrappers.

Section labels (`.label`) carry a default `--space-2` (6.8px) bottom margin — tight by design, so a stack of KPI tiles doesn't read as padded.

---

## 05 · Component Vocabulary

**Buttons & tags**

| Variant | Style | Class |
|---|---|---|
| Primary | accent fill | `.btn .btn-primary` |
| Secondary | neutral | `.btn` |
| Ghost | outline | `.btn` |
| Tag | accent | `.tag .tag-accent` |

**KPI tile** — blueprint frame · label / figure / delta

```
REVENUE (TTM)
$92.8B
▲ +33.8% YoY
```

**Data table** — `.table`, numbers right-aligned and tabular, compact row padding (`5px 10px`)

| METRIC | Q3'25 | Q2'25 | YOY |
|---|---:|---:|---:|
| Revenue | $24.8B | $22.7B | +33.8% |
| Free cash flow | $8.2B | $4.4B | −4.2% |

**Tabs** — e.g. `OVERVIEW` · `FINANCIALS`

**Filter field** — `.field .input` (e.g. "Min YoY growth" → `+20%`)

---

## 06 · Layout Recipe Per App Type

Pick a recipe by **interaction pattern**, not by subject matter. A page that lists tickers isn't automatically Tracker — Tracker is specifically for date/calendar-grid data. A sortable watchlist or discovery table is Screener or List/Builder. If a page genuinely matches none of the five below, define a new named recipe rather than forcing the nearest one — that's how Upload/Analyze below got added.

**Dashboard / detail** *(e.g. Stock Dashboard)*
Title + price/status row → tabs → KPI tile row → chart panel + profile aside (2.1 : 1).

**Screener** *(e.g. Taiwan Revenue · Parabolic)*
Filter aside (260px, blueprint) + results table. Primary "Run / Scan" button in the aside.

**Tracker / calendar** *(e.g. Earnings Tracker)*
Week columns of blueprint day-cells (today tinted `accent-100`) → "already reported" results table below.

**List / builder** *(e.g. Indexer)*
Master table (1.5) + detail aside (1) with a headline figure and holdings list. "New" primary button in the header.

**Upload / analyze** *(e.g. Options Position Analyzer)*
Title + status row → dropzone (blueprint, collapses to a slim "add another" bar once data is loaded) → KPI tile row → full-width chart panel → results table below. For tools where the input is a file/screenshot rather than a filter or ticker search.

---

## 07 · Converting an Existing App

Converting means full replacement, not coexistence. Adopting this spec retires any prior theme, palette, or component library entirely — there is no compatibility mode that keeps old branding alongside the blueprint system. If that's a real concern for a given app (existing users expect the old look, etc.), raise it before starting; it's not something to solve mid-conversion.

### Step 0: inventory before touching anything

Before converting *or* rebuilding, have the coding agent read the current codebase — not recall the original prompt — and produce a written inventory: every route, every data flow, every business rule, every validation and edge case it can find. An app built across many incremental sessions has no single prompt that captures what it actually does; the real spec is the accumulated code. This inventory becomes the acceptance checklist for whichever path you take next.

**Retrofit vs. rebuild:** default to retrofitting incrementally — the eight numbered steps below, one at a time, with a build/test and a git commit after each. This keeps the blast radius small and every step revertible. Reach for a full ground-up rebuild only if the inventory pass shows the old theme is genuinely inseparable from the business logic throughout (colors/spacing hardcoded per-component rather than tokenized, or a different component library woven through the data layer) — in that case a "conversion" would just be a rewrite fought against existing structure anyway. Either way, check the result against the Step 0 inventory before calling it done; that's what actually prevents silent regressions, not the choice of retrofit vs. rebuild itself.

1. Link `styles.css`; wrap the app in the global top bar + context bar.
2. Swap every font to Inter — all text and numbers, no exceptions; uppercase every heading.
3. Recolor to tokens only — kill every stray hex, gradient and shadow.
4. Reframe every card/panel as `.blueprint` with corner marks; square all radii.
5. Right-align numeric columns; set `tabular-nums`.
6. Gains → `#2e8b57`, losses → `#c0392b`. Nothing else colored.
7. Delete descriptions and sell copy; labels become short uppercase.
8. Buttons → `.btn`; inputs → `.input`; tables → `.table`.

**Acceptance test:** put the converted app beside the Stock Dashboard. If the top bar, type, frames and number treatment are indistinguishable and only the content differs, it passes visually — but also re-check it against the Step 0 inventory to confirm nothing functional was lost along the way.
