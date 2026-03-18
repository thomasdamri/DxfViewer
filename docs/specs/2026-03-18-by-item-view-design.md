# By Item View — Design Decisions

**Date:** 2026-03-18
**Status:** Direction chosen, not yet implemented in production panel

---

## Context

The defect panel has two list views toggled by "By Defect / By Item" in the toolbar chrome. The panel is scoped to a **system/drawing context** — it shows defects across *multiple* fittings, not a single fitting. The user can then navigate to an individual fitting's view separately.

**Primary use case for By Item:** Research. Engineers open this view when investigating a live defect and want to understand the defect history of the fittings in that system — which assets have been problematic before, how many times, and what happened. This is a lookup/reading workflow, not an urgency triage.

---

## Approach chosen: Item master card + inline rows (Approach C)

Three approaches were considered:

| | Approach | Description |
|---|---|---|
| A | Accordion groups + compact rows | Items collapse/expand. Children are dense single-line rows with left-border classification colour. |
| B | Section dividers + full cards | Sticky asset headers divide the list. Cards are slimmer than By Defect but still show full title and tags. |
| **C** | **Item master card + inline rows** | Each asset is a card. Defects appear as compact rows inside the card. Expand/collapse at item level. |

**Rationale for C:** Clear card boundary per asset. The left border on the master card gives instant classification triage at item level without expanding. Inline rows are dense enough for research scanning. The pattern is familiar — cards within a card — and doesn't introduce new interaction patterns.

---

## Key decisions

### Default expand/collapse state: All expanded

Initial instinct was "all collapsed" (good for urgency triage — scan asset list, open what matters). Reversed after clarifying the primary use case.

**Why expanded:** In a research session the user is *reading*, not *scanning for urgency*. Hiding history behind a tap is friction against the primary job. If the list gets long, the user can collapse what they don't need — that's a better default than forcing them to expand everything to start reading.

### Click on inline defect row: Opens detail view directly

No expand-in-place step. The DEF ID is always fully visible in the row and uniquely identifies the record. Adding an intermediate expand to show the full title — which is already on the detail screen — is extra friction with no payoff. Consistent with By Defect behaviour: same tap, same result, regardless of toggle state.

### Result count: `N items · N defects`

Shows both dimensions. In a research session the engineer wants to know how many distinct assets have a history here *and* the total volume of defects. "3 items" alone loses the defect count; "7 defects" alone loses the asset grouping signal.

---

## Item card structure

```
┌─────────────────────────────────────────────────────┐
│ [border] CV-100-XF                              ●● 2 │
│          Crossfeed valve                             │
│  ─────────────────────────────────────────────────  │
│  DEF-00421/A  Valve handle misalignment     [LIVE]   │
│  DEF-00389/B  Pressure seal degradation     [LIVE]   │
└─────────────────────────────────────────────────────┘
```

- **Left border (3px):** Classification colour of the highest-priority open defect on that asset
- **Dot indicators:** One dot per defect, coloured by classification — gives volume + mix at a glance without expanding
- **Inline rows:** DEF ID (mono) · truncated title · status badge. One tap → detail view.
- **Expand/collapse toggle** on the asset card header

---

## What's out of scope here

The individual fitting view (drilling into a single fitting's full defect history) is a separate panel/screen. By Item is still the "all defects in this system context" view, just reorganised by asset. A separate design session covers the fitting-specific view.

---

## Prompts & artefacts

- Visual mockups generated in browser during brainstorming session (2026-03-18)
- Prompt for individual fitting view design session: see conversation history 2026-03-18
