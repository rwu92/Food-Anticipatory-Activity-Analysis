# FAA Activity Analyzer

A single-file, offline tool for analyzing food-anticipatory activity (FAA) from Oxymax/CLAMS locomotor data. Open `faa_analyzer.html` in any browser — no server, no internet connection, and no data ever leaves your machine.

## What it does

For each subject, the tool:

1. Sums `XTOT + ZTOT` at every recorded timestamp to get an activity value.
2. Buckets those values into calendar days.
3. Defines a **pre-meal window** — a configurable number of hours immediately before a configurable mealtime — and sums activity within that window.
4. Reports pre-meal activity as a **fraction of that day's total activity**, per subject, per day.

This lets you see whether activity is anticipatory (clustered right before feeding) rather than just generally elevated.

## Uploading data

Drag files onto the dropzone, or click it to browse. You can add more files at any time — they're merged by **Subject ID**, so multiple recording weeks for the same subject combine into one continuous trace.

**Supported formats:**
- `.csv` — raw Oxymax/CLAMS export (`Subject ID` / `Group-Cage` header rows, then an `INTERVAL,CHAN,DATE/TIME,...,XTOT,ZTOT` table)
- `.xlsx` / `.xls` — same, or a workbook with one sheet per subject

The tool looks for the `INTERVAL,CHAN,DATE/TIME` header row and the `XTOT`/`ZTOT` columns automatically; everything above/around them (metadata, unit rows, separator rows) is ignored.

## Settings

| Setting | What it does |
|---|---|
| **Mealtime** | Clock time the pre-meal window counts back from |
| **Pre-meal window (hrs)** | Length of the pre-meal window |
| **Show points on trend charts** | Toggle point markers on the two trend charts — turn off when you have many days plotted and it looks cramped |
| **Ignore partial days** | Excludes any day where the pre-meal window wasn't fully captured (recording start/end days, or gaps in a recording) from the charts, table, and copy/export data |

All settings recompute automatically — no "apply" button needed.

## Reading the charts

- **Total Daily Activity** — one line per subject, total counts per day. Days with a full pre-meal window are filled circles; partial/leftover days are hollow ✕ markers (unless "Ignore partial days" is on, in which case they're dropped entirely).
- **Pre-meal Activity as % of Daily Total** — only plots days with a fully-captured pre-meal window, since a partial window can't give a meaningful fraction.
- **Activity Traces** — the raw per-subject signal over the whole recording period, with the pre-meal window and mealtime shaded/marked on each full-window day.

A day only counts as having a "full window" if there's continuous data from the start of the pre-meal window through mealtime — no significant gaps.

## Getting data out

- **Export CSV** — the full daily summary table (subject, cage, date, day total, pre-meal activity, % of day, window status).
- **Copy Total Activity** / **Copy Pre-meal % (Normalized)** — puts a tab-separated table on your clipboard: one row per date, one column per subject. Paste directly into Prism (or Excel/Sheets) as an XY table.

## Clear all

Wipes every loaded file and resets the tool to its empty state.

## Notes

- Everything runs client-side — Chart.js and the Excel-reading library (SheetJS) are bundled into the HTML file itself, so it works with no network access.
- If a file fails to parse, it shows up in the file list with an error message instead of silently disappearing; click the ✕ next to it to remove it.
- The "median interval" (used to judge whether a pre-meal window was fully captured) is computed per subject from the actual gaps between consecutive readings, so it adapts automatically whether your data was logged every 10 minutes or some other interval.
