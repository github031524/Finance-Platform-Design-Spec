# NC Futures — Platform Design Specification

**Version 1.0 · Industry System**

> One look for every app. This is the contract every NC Futures module follows. Each app keeps its own logic and data — but adopts the same shell, tokens, type, and component vocabulary defined here, so five separate tools read as one platform. Load the one stylesheet, follow the five rules, and reuse the markup below verbatim.

---

## 01 · The Five Rules

1. **Blueprint frames** — Every card, tile, panel and figure is a square, hairline-bordered line drawing with `+` corner marks. No rounded corners, no filled surfaces, no drop shadows as decoration.
2. **One color** — Steel blue is the only accent. Gains use the deep accent step; losses use the one muted terracotta. Nothing else is colored.
3. **One typeface: Inter** — Inter for everything, all text and all numbers, no exceptions. Headings and figures use 600 weight uppercase; body is 400/500. Numbers are tabular.
4. **Visible grid** — Equal cells, hairline dividers, strong horizontal and vertical rhythm. Structure is drawn, not implied by whitespace alone.
5. **Data first** — No marketing copy inside the tool. Labels are short and uppercase; the numbers are the loudest thing on screen.

**The one exception:** the primary button is the single solid object — an accent fill that still keeps square corners and corner marks.

---

## 02 · Foundations — Tokens

### Color

| Token | Value |
|---|---|
| `bg` | `#f2f2f3` |
| `surface` | `#e9e9ea` |
| `text` | `#1d1f20` |
| `accent` | `#5980a6` |
| `gain` | `accent-700` |
| `loss` | `#a6595e` |

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
| KPI figure | 27px |
| Body | 13–15px |
| Label | 10.5px, uppercase, 0.13em tracking |
| Micro | 9–10px |

All numeric cells use `font-variant-numeric: tabular-nums`.

### Spacing & grid

- Space tokens `--space-1` … `--space-8` (3.4 → 27px, a 0.85× scale)
- Radius is `0` everywhere (square)
- Content column max-width: `1320px`
- Gutter: `clamp(16px, 3vw, 32px)`
- Card / tile grid gap: `clamp(12px, 1.5vw, 20px)`

---

## 03 · The Shell — Chrome Every App Inherits

Three fixed layers wrap every module. An app only ever supplies the content region; the top bar and context bar are identical across all apps.

**A · Global top bar** — 62px, sticky, hairline base.
`NC FUTURES ▦ · Modules ▾ · Search any ticker… ◌ · NC`

**B · App context bar** — breadcrumb + status, surface fill.
`Workspace / Module name` · `Shared login · unified data`

**C · Content region** — the only part an app owns.

```html
<header>…global top bar…</header>

<div> <!-- app context bar -->
  Workspace / [App name] [status tag]
</div>

<main style="max-width:1320px; margin:0 auto;
      padding:clamp(22px,3vw,40px) clamp(16px,3vw,32px);">
  … APP CONTENT GOES HERE …
</main>
```

---

## 04 · The Blueprint Frame — The Signature

```html
<div class="blueprint" style="padding:20px">
  <i class="corner tl"></i><i class="corner tr"></i>
  <i class="corner bl"></i><i class="corner br"></i>
  … content …
</div>
```

The `.blueprint` class draws the square hairline border; the four `<i class="corner">` children draw the registration marks. **Never drop a corner.** Use it on tiles, KPI cards, chart panels, filter asides and table wrappers.

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

**Data table** — `.table`, numbers right-aligned and tabular

| METRIC | Q3'25 | Q2'25 | YOY |
|---|---:|---:|---:|
| Revenue | $24.8B | $22.7B | +33.8% |
| Free cash flow | $8.2B | $4.4B | −4.2% |

**Tabs** — e.g. `OVERVIEW` · `FINANCIALS`

**Filter field** — `.field .input` (e.g. "Min YoY growth" → `+20%`)

---

## 06 · Layout Recipe Per App Type

**Dashboard / detail** *(e.g. Stock Dashboard)*
Title + price/status row → tabs → KPI tile row → chart panel + profile aside (2.1 : 1).

**Screener** *(e.g. Taiwan Revenue · Parabolic)*
Filter aside (260px, blueprint) + results table. Primary "Run / Scan" button in the aside.

**Tracker / calendar** *(e.g. Earnings Tracker)*
Week columns of blueprint day-cells (today tinted `accent-100`) → "already reported" results table below.

**List / builder** *(e.g. Indexer)*
Master table (1.5) + detail aside (1) with a headline figure and holdings list. "New" primary button in the header.

---

## 07 · Converting an Existing App

1. Link `styles.css`; wrap the app in the global top bar + context bar.
2. Swap every font to Inter — all text and numbers, no exceptions; uppercase every heading.
3. Recolor to tokens only — kill every stray hex, gradient and shadow.
4. Reframe every card/panel as `.blueprint` with corner marks; square all radii.
5. Right-align numeric columns; set `tabular-nums`.
6. Gains → `accent-700`, losses → `#a6595e`. Nothing else colored.
7. Delete descriptions and sell copy; labels become short uppercase.
8. Buttons → `.btn`; inputs → `.input`; tables → `.table`.

**Acceptance test:** put the converted app beside the Stock Dashboard. If the top bar, type, frames and number treatment are indistinguishable and only the content differs, it passes.
