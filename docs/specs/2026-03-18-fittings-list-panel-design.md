# Fittings List Panel — Design Spec
**Date:** 2026-03-18
**File:** `fitting_panel_variants.html`

---

## Context

The Fittings panel is a ~315px left sidebar alongside the system diagram canvas. It shows a navigable list of physical components (valves, pipes, tanks, etc.) grouped by system. Selecting a fitting in the panel highlights it on the canvas; clicking a canvas node selects it in the panel. A tooltip on the canvas shows basic fitting info with a "View details" link.

The existing panel uses a 3-level collapsible tree (System → Function → Fittings) with a basic search bar. This spec resolves three design problems: search behaviour, system filtering, and hierarchy depth.

---

## Decisions

### 1. Hierarchy — two-level: System groups → fitting rows

Remove the Function sub-group level. Replace with:

- **System group headers** — collapsible, with colour dot + system name + count badge
- **Fitting rows** — fitting ID (mono) + name (sans) + function as a small muted tag on the right

```
▾ ● Vantor Engine Core                    14
    001 · Crossfeed valve        [Valves]
    002 · Butterfly valve        [Valves]
    006 · Main feed pipe         [Pipes]
▾ ● Helix Propulsion Suite                 3
    007 · Thrust nozzle          [Nozzles]
    …
```

**Rationale:** 3-level nesting is too deep at 315px. System grouping is meaningful and mirrors canvas colour coding — the panel and diagram stay spatially coherent. Function as a tag preserves classification context without adding a third collapsible level.

---

### 2. Search — flatten to flat results

During search the hierarchy collapses entirely. Results show as a flat list:

- **Primary line:** fitting ID (mono) + fitting name, with matched substring highlighted (background `#E6F1FB`, text `#185FA5`)
- **Secondary line:** system colour dot (5px) + "System name · Function" in IBM Plex Mono 10px muted
- **Count row** above results: e.g. "7 results" in mono muted, on `--bg-secondary` strip
- **Clear button** (×) in the search input restores the tree

Non-matching fittings are hidden entirely — no dimming, no group headers.

Filters on:
- Fitting ID (e.g. `001`, `CV-100-XF`)
- Equipment name (e.g. `valve`, `pump`, `crossfeed`)

**Rationale:** Hierarchy adds noise during search. Context travels per-row as secondary metadata. Clearing the search restores the full tree exactly as left.

---

### 3. System filter — not added

No system filter control is included. The two-level collapsible tree covers the same use case: collapsing unwanted system groups takes at most N−1 clicks and is already familiar from the hierarchy interaction.

**Primary user flow:** users arrive at the panel via canvas click, already system-scoped. A system filter optimises a browse-first flow that is secondary.

**Deferred option:** if users report needing to isolate 2–3 systems from 20+ without individually collapsing others, add a "Systems ▾" popover button (see Option 2B in `fitting_panel_variants.html`). It adds one toolbar row and does not require restructuring the panel.

---

## Component spec

### Panel chrome
```
[ Fittings ]     ← IBM Plex Mono 11px, 500 weight
```
Border-bottom 0.5px, padding 9px 12px.

### Toolbar
```
[ ⌕  Search fittings…                    ]
  12 fittings
```
Search input: `--bg-secondary`, border-radius 6px, height 28px, IBM Plex Sans 12px.
Result count row: mono 10px muted, only shown when a filter or search is active. Hidden in default state (or shown always — implementation choice).

### System group header
| Property | Value |
|---|---|
| Chevron | 9px, `--text-tertiary`, rotates −90° when collapsed |
| Colour dot | 8px circle, system colour |
| Name | IBM Plex Sans 12px 500, `--text-primary`, truncate with ellipsis |
| Count badge | IBM Plex Mono 10px, `--text-tertiary` |
| Padding | 7px 12px |
| Border-bottom | 0.5px `--border` |
| Hover | `--bg-secondary` background |

### Fitting row (inside system group)
| Property | Value |
|---|---|
| ID | IBM Plex Mono 11px, `--text-secondary`, flex-shrink 0 |
| Name | IBM Plex Sans 12px, `--text-primary`, flex 1, truncate |
| Function tag | IBM Plex Mono 10px, `--text-tertiary`, `--bg-secondary` bg, 0.5px border, border-radius 3px, padding 1px 5px |
| Indent | padding-left 28px |
| Border-bottom | 0.5px `--border` (last child: none) |
| Hover | `--bg-secondary` background |

### Search result row
| Property | Value |
|---|---|
| Dot | 7px circle, system colour, margin-top 4px (aligns to text cap) |
| ID | IBM Plex Mono 11px, `--text-secondary` |
| Name | IBM Plex Sans 12px, `--text-primary`; matched span: bg `#E6F1FB`, text `#185FA5`, border-radius 2px |
| Secondary line | Mono 10px, `--text-tertiary`; system dot 5px + "System · Function" |
| Padding | 7px 12px |
| Border-bottom | 0.5px `--border` |

---

## System colours

| System | Colour | Usage |
|---|---|---|
| Vantor Engine Core | `#52A688` | Dot, canvas nodes |
| Helix Propulsion Suite | `#C4966A` | Dot, canvas nodes |
| Stratus Power Unit | `#D96B6B` | Dot, canvas nodes |
| Aurex Drive System | `#9490C2` | Dot, canvas nodes |

New systems get a new colour entry — same extensibility pattern as the classification system.

---

## Style rules (from CLAUDE.md)

- Fonts: IBM Plex Sans (body), IBM Plex Mono (IDs, labels, tags, metadata)
- CSS variables throughout — no hardcoded colours except system dot vars
- Flat, clean, light theme
- No gradients, no shadows, border-radius ≤ 12px

---

## Mockup reference

`fitting_panel_variants.html` — all explored variants as labelled 315px panel mockups:
- **1A/1B** — search default and mid-search states
- **2A/2B/2C** — system filter options (chips / popover / sc-pill row)
- **3A/3B** — flat list vs two-level hierarchy
- **★** — combined recommendation
