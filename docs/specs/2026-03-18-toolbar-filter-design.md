# Toolbar Filter Design — List View Chrome

**Date:** 2026-03-18
**File:** `toolbar-filter-mockups.html`
**Status:** Direction chosen, not yet implemented in production panel

---

## Context

The defect tracking panel is a 350px-wide web component with a list view and a detail view. The list view has a chrome toolbar above the defect cards. This spec covers the toolbar's filter controls, specifically how users select from a keyword taxonomy to narrow the defect list.

---

## Toolbar Structure

Four rows in order, below the panel title bar (asset tag + tabs):

```
Row 1: [By Defect  |  By Item]          ← segmented toggle, full width
Row 2: [N results] [──search──] [Priority ↓] [Filters ▾]
Row 3: [All]  [Live N]  [Closed N]      ← status filter chips (pill-shaped)
Row 4: (conditional) active filter chip strip — appears only when filters active
```

Row 4 is the main design decision of this document.

---

## The Filter Problem

There are ~25 keyword identifiers used to tag defects:

> mechanical-wear, alignment, corrosion, seal-failure, valve-seating, pressure-loss, thread-wear, actuator, isolation, vibration, fatigue, thermal-expansion, erosion, fouling, cavitation, leakage, cracking, deformation, electrical-fault, instrument-drift, calibration, blockage, coating-failure, material-defect, structural

These are **multi-select**: an engineer might filter by `corrosion + leakage` to investigate a failure category. The system should also accommodate additional filter types in future (e.g. Classification, Discipline, Reporter) without requiring new toolbar buttons.

---

## Patterns Explored

Three interaction patterns were mocked up and evaluated. All were rendered at panel width (350px) with default / open / selected states.

### Pattern A — Dropdown with search + checkboxes

Button `[Keywords ▾]` in the toolbar row opens a positioned dropdown with:
- Search input at top
- Scrollable checkbox list (alphabetical)
- Checked items float to top when selections exist

**Trade-offs:**
- Familiar (standard filter control paradigm)
- Search reduces interaction cost at 25+ items
- Covers list content while open — user loses context of what they're filtering
- Active state hidden in button label count (`Keywords (2) ▾`)

### Pattern B — Inline expand (chip grid)

Button `[Keywords ▾]` expands a band below the toolbar showing all 25 keywords in a 2-column chip grid.

**Trade-offs:**
- Nothing hidden in a popover
- All options scannable simultaneously
- 2-column grid of 25 items is ~330px — nearly half the 680px panel height
- Pushes card list significantly downward
- Too spatially expensive for a secondary control; ruled out

### Pattern C — Transient popover + active chip strip ✓

Button `[Filters ▾]` opens a transient popover. After selection, the popover closes and a chip strip (Row 4) appears below the status chips showing active selections as dismissible chips.

**Trade-offs:**
- Active filter state always visible — engineers returning to a filtered view never forget what's filtering
- Chip strip only appears when active (zero-cost default state)
- Popover is transient — no explicit dismiss, closes after interaction
- Extensible (see below)

---

## Chosen Design — Pattern C with Cross-Section Search

The final design merges A's search mechanism into C's architecture.

### The key insight

Pattern A's search and Pattern C's active chip strip solve *different problems* and are not mutually exclusive:

- **How you pick** (search + chips in popover) ← from A
- **How you show active state** (persistent chip strip) ← from C

### Popover structure

```
┌─────────────────────────────────────┐
│ ⌕  Search filters…                  │  ← single search input
├─────────────────────────────────────┤
│ KEYWORDS  25                        │  ← section label (mono 9px uppercase)
│ [actuator] [alignment] [blockage]   │
│ [calibration] [cavitation] …        │  ← flex-wrap chips
│                                     │
│ CLASSIFICATION  5          (future) │
│ [Essential] [Routine] …             │
└─────────────────────────────────────┘
```

The search input filters **across all sections simultaneously**. When "fail" is typed:
- Keywords section narrows to: `coating-failure`, `electrical-fault`, `seal-failure` (3 of 25)
- Classification section shows: `0 of 5 · No matches` (section header remains visible)

This cross-section behaviour is the primary extensibility argument for this pattern.

### Active chip strip (Row 4)

Appears below Row 3 when any filter is active. Disappears entirely when all chips are dismissed.

```
KW: [mechanical-wear ×]  [alignment ×]
```

- Label prefix (`KW:`) identifies the filter type — when multiple types are active, each type gets its own label prefix or grouping
- Each chip: `background: #E6F1FB; color: #185FA5; border: 0.5px solid #B5D4F4` (routine/blue tint)
- `×` dismiss removes the individual selection; if all removed, Row 4 disappears

### Button label

- Default: `Filters ▾`
- Open: `Filters ▾` (button has slightly darkened background)
- Active: `Filters (2) ▾` (blue tint: `background: #E6F1FB; color: #185FA5`)

The button was deliberately renamed from `Keywords ▾` to `Filters ▾` to reflect the multi-type intent from the start.

---

## Extensibility Model

When a new filter type is added (e.g. Classification):

1. Add a new section to the popover (section label + chip grid)
2. The cross-section search already handles it — no changes to search logic
3. Active selections from the new type appear in Row 4 with their type prefix
4. No new toolbar button required; `Filters ▾` remains the single entry point

This means the toolbar chrome **does not grow** as filter types are added. The `Filters (N) ▾` count reflects total active filters across all types.

---

## Design System Constraints

All toolbar controls follow the rules in `CLAUDE.md`:

- Fonts: IBM Plex Sans (labels, buttons), IBM Plex Mono (counts, IDs, chip text)
- No gradients, no shadows, no `border-radius` > 12px
- CSS variables throughout — no hardcoded colours except the classification system
- Active/routine blue: `#185FA5` (bg: `#E6F1FB`, border: `#B5D4F4`)
- Border standard: `0.5px solid rgba(0,0,0,0.11)`
- Border medium (buttons, dropdowns): `0.5px solid rgba(0,0,0,0.18)`

### Button heights

All controls in Row 2 are `height: 26px` with `border-radius: 5px`. The segmented toggle (Row 1) uses `border-radius: 6px` with joined borders between segments.

### Status filter chips (Row 3)

Pill-shaped (`border-radius: 100px`), IBM Plex Mono 10px. Active = dark fill. When "Live" or "Closed" chip is active, use the LIVE/CLOSED badge colours from the status badge spec rather than the default dark fill — this mirrors the card badge colours and reinforces the visual language.

---

## Files

| File | Purpose |
|------|---------|
| `toolbar-filter-mockups.html` | Static mockup — 3 patterns (A, B, C) + complete assembled panel |
| `defect_panel_left.html` | Existing list + detail view panel (CSS variables source of truth) |
| `CLAUDE.md` | Design system rules, classification colours, sample data |

The mockup file is self-contained (Google Fonts only external dependency) and can be opened directly in a browser for evaluation.

---

## Open Questions

- Should the chip strip (Row 4) wrap to multiple lines if many filters are active, or truncate with an overflow indicator?
- When the popover is open, does clicking outside dismiss it, or does it require an explicit close action?
- Priority sort dropdown (Row 2): is this a separate concern or should it eventually fold into the Filters popover?
