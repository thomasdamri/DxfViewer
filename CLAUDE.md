# Defect Tracking Panel UI

Web app panel component. 350px+ wide. Two views: list and detail. Clicking a card switches the panel to the detail view.

## Stack & aesthetic

- Fonts: IBM Plex Sans (body), IBM Plex Mono (IDs, labels, tags, metadata)
- Style: flat, clean, light theme
- CSS variables throughout — no hardcoded colours except the classification system below
- No gradients, no shadows, no border-radius above 12px

## Classification system

Left border (3px) + label text colour. Extensible — new values just need a colour entry.

| Classification | Colour    |
| -------------- | --------- |
| Critical       | `#A32D2D` |
| Essential      | `#BA7517` |
| Routine        | `#185FA5` |
| Monitoring     | `#0F6E56` |
| Deferred       | `#888780` |

Classification is a **triage/work category**, not a severity level. Do not treat it as low/medium/high.

## Status badges

Pill-shaped, monospaced font, small.

| Status    | Background | Text                 |
| --------- | ---------- | -------------------- |
| LIVE      | `#FCEBEB`  | `#A32D2D`            |
| SCHEDULED | `#E6F1FB`  | `#185FA5`            |
| CLOSED    | `#F1EFE8`  | `#888780` (+ border) |

## List view

### Chrome (top of panel)

- Panel title: asset tag · asset name (e.g. `FV-101 · Crossfeed valve`)
- Tabs: General / Defects / History — Defects tab shows a count badge
- Toggle: By Defect / By Item
- Row: result count · search input · Priority sort dropdown · Keywords filter dropdown
- Filter chips: All / Live N / Closed N

### Card layout

```
DEF-XXXXX/X · Classification          [STATUS]
Title of the defect
Asset tag · Asset name
[tag] [tag]                    X other fittings →
```

- Left border colour = classification colour
- Classification label colour = classification colour
- Tags: small, muted, rounded, light background
- "X other fittings →" right-aligned, blue, only shown when relevant

## Detail view

Same panel header chrome (title + tabs) stays visible — user can navigate back without a back button.

### Header block

- DEF ID · Classification · status badge
- Title
- Asset tag · Asset name
- Tags

Header top border = classification colour (3px).

### Sections (in order)

1. Defect Description
2. Defect Nature
3. Solution
4. Supporting Document — UNC path in mono font + copy-to-clipboard button
5. Reported Details — 2-column grid: Date Reported, Reported By, Discipline, Location
6. Also Affected — lettered list (A, B, C…), current item has "current" badge, long asset tags truncate with ellipsis

Section labels: IBM Plex Mono, 10px, uppercase, tracked, muted.

## Sample data

### DEF-00421/A

- Classification: Essential · Status: LIVE
- Title: Valve handle exhibits progressive misalignment during actuation
- Asset: CV-100-XF · Crossfeed valve
- Tags: mechanical-wear, alignment
- Also affected: A — CV-100-XF (current), B — CV-101-YZ, C — CV-102-AB, D — CV-103-FD-CROSSFEED-SECONDARY-BYPASS, E — CV-104-GK
- Reporter: J. Smith, Mechanical Engineer, Burton upon Trent, 15 Jan 2025
- Doc: `\\network-share\engineering\defects\DEF-00421\Valve_Handle_Misalignment_Report.pdf`

### DEF-00389/B

- Classification: Essential · Status: LIVE
- Title: Pressure seal degradation at primary outlet causing intermittent leakage
- Asset: CV-100-XF · Crossfeed valve
- Tags: corrosion, seal-failure
- Other fittings: 2

### DEF-00389/B (second)

- Classification: Routine · Status: SCHEDULED
- Title: Pressure relief valve fails to reseat after actuation
- Asset: PRV-204-AK · Relief valve, north header
- Tags: valve-seating, pressure-loss

### DEF-00451/A

- Classification: Deferred · Status: CLOSED
- Title: Actuator stem thread wear on quarter-turn isolation valve
- Asset: ISV-087-MN · Isolation valve, feed line B
- Tags: thread-wear, actuator, isolation
