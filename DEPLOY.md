# Deploying — GitHub Pages and the Google Sheet

## Part 1 — Publish the dashboard on GitHub Pages

Your repository is `ankur99119/delhi-division-dashboard` and it already serves
`index.html` from the `main` branch.

**Files to upload (all four, at the repository root):**

```
index.html                              the dashboard
gate-data.js                            359 level-crossing gates  ← exact filename, hyphenated
assets/delhi-division-system-map.jpg    source drawing, full division
assets/delhi-area-map.jpg               source drawing, Delhi detail
```

> The page loads the gate file by the exact name `gate-data.js`. If it is named
> `gatedata.js` the request 404s and **all 359 gates disappear silently**. The
> build now raises an on-screen warning if that happens, but the filename must
> still be right.

**Steps in the browser (no Git needed):**

1. Open `https://github.com/ankur99119/delhi-division-dashboard`
2. **Add file → Upload files**
3. Drag in `index.html` and `gate-data.js`
4. Drag the `assets` folder in as well — GitHub keeps the folder structure
5. Scroll down, write a commit message such as `v3 — gates, unified map, fixes`
6. Choose **Commit directly to the main branch**, then **Commit changes**
7. **Settings → Pages** → Source: *Deploy from a branch* → Branch: `main`, folder `/ (root)` → **Save**

The site appears at `https://ankur99119.github.io/delhi-division-dashboard/`
within a minute or two. GitHub Pages gzips the HTML automatically, so the
432 KB file transfers as about 136 KB.

**After it goes live, check three things:**

- The map shows level-crossing marks (if not, the `gate-data.js` filename is wrong)
- The header reads a station count, gate counts, and "Updated <time>"
- Press `+` a few times — labels should thin out and stay legible, never overlap

**Updating later:** upload the changed file again and commit. Hard-refresh with
`Ctrl+Shift+R` (Windows) or `Cmd+Shift+R` (Mac); the browser caches aggressively.

---

## Part 2 — The Google Sheet

### Already done for you

| Change | Where |
|---|---|
| Backup copy taken before any edit | `BACKUP 2026-08-15 - Delhi Division TI Dashboard…` in your Drive |
| `TI Name` formula rebuilt — 13 explicit branches, DELHI added, unknown codes blank | INSPECTIONS column F |
| Chirag Vats's 2 misattributed rows corrected (49 → 51) | INSPECTIONS |
| `Time (Day/Night)` column inserted with a Day/Night dropdown | INSPECTIONS column H, rows 5–1000 |
| Inspection Type dropdown extended with Station / Gate / Footplate / Road Inspection | INSPECTIONS column G, rows 5–1000 |
| `TI Name` for `DEC` set to **Shankar Lal Meena** on every row, past and present | INSPECTIONS column F, rows 5–1000 |

The DEC branch is no longer date-aware. April–July rows that used to read
Kailash Babu now read Shankar Lal Meena, which is what "change it everywhere"
means and what keeps the sheet and the dashboard agreeing.

### How to record from August 2026

The new scheme is live from **Aug-26**. For every new row: pick the
**Inspection Type** (the subject only — `Gate Inspection`, not `Night
Inspection - Level Crossing`) and then pick **Day** or **Night** in the Time
column.

| Inspection Type | Day | Night |
|---|---:|---:|
| Safety Inspection | 5 | 6 |
| Station Inspection | 4 | 5 |
| Footplate Inspection | 4 | 5 |
| Gate Inspection | 3 | 4 |
| Road Inspection | 3 | 4 |

April–July rows keep the old scheme and old values; nothing before August moves.

**What moving the cutover to August changes for rows already entered:** August
rows typed `Night Inspection - Station` now score 5 instead of 4, and any August
`Footplate Inspection` row that scored 0 under the old list now scores 4. If an
August row's Time cell is blank, the type name still decides day or night, so
nothing scores wrongly while you backfill the column.

### The KPI tracker tabs — rebuilt and verified

All 13 `TI_*` tabs now score the new scheme from Aug-26. Three defects were
found and fixed.

**1. `TI_FDB!E9` held a hardcoded `0` instead of its `COUNTIFS`.** April Safety
Inspections read 0 when the real count is 8 — TI/FDB was short **40 points for
April**. Month total 16 → 56, annual points 110 → 150.

**2. `Joint Footplate Inspection` had no row.** There are 21 such records in
INSPECTIONS and every tab was scoring all of them **zero**. This predated the
new scheme; it was wrong under the old rules too.

**3. No Gate row and no day/night split.** From Aug-26 the dashboard scored gate
work and the night bonus and the tracker did not.

`I15:P15` (August to March) on each tab now holds one self-contained formula
that reads the TI code from `B3` and the month from row 7, so the same text
works on every tab:

```
=IFERROR(LET(t,TRIM(MID($B$3,FIND("/",$B$3)+1,20)),m,I$7,
 c,LAMBDA(ty,COUNTIFS(INSPECTIONS!$E:$E,t,INSPECTIONS!$D:$D,m,INSPECTIONS!$G:$G,ty)),
 b,LAMBDA(ty,COUNTIFS(INSPECTIONS!$E:$E,t,INSPECTIONS!$D:$D,m,INSPECTIONS!$G:$G,ty,INSPECTIONS!$H:$H,"")),
  c("Safety Inspection")*5
 +(c("Surprise Inspection")+c("Night Inspection - Station")+c("Station Inspection"))*4
 +(c("Night Inspection - Level Crossing")+c("Gate Inspection"))*3
 +(c("Night Inspection - Footplate (Goods)")+c("Joint Footplate Inspection")+c("Footplate Inspection"))*4
 +(c("Surprise Road Inspection")+c("Road Inspection"))*3
 +COUNTIFS(INSPECTIONS!$E:$E,t,INSPECTIONS!$D:$D,m,INSPECTIONS!$H:$H,"Night")
 +b("Night Inspection - Level Crossing")+b("Night Inspection - Station")
 +b("Night Inspection - Footplate (Goods)")),0)
```

The last three terms give the +1 night bonus to legacy `Night Inspection - …`
rows whose Time cell is still blank, exactly as the dashboard does.
`E15:H15` (April–July) were **not touched**, so no historical figure moved.

**How it was verified.** Before making any change I pulled the August record
counts straight from INSPECTIONS, grouped by TI, type and time, and worked out
by hand what each tab should read. Every one of the 13 tabs then matched:

| TI | Aug before | Aug after | why it moved |
|---|---:|---:|---|
| FDB | 21 | 21 | — |
| DLI | 4 | 5 | night bonus |
| DELHI | 14 | 14 | — |
| MUT | 31 | 33 | night bonus ×2 |
| SMQL | 38 | 40 | night bonus ×2 |
| NDLS | 10 | 10 | — |
| ROK | 21 | 25 | Joint Footplate now counts |
| JHI | 23 | 23 | — |
| JHL | 20 | 22 | night bonus ×2 |
| SNP | 20 | 21 | night bonus |
| KKDE | 20 | 20 | — |
| PNP | 0 | 0 | — |
| DEC | 9 | 9 | — |

`KPI_SCORES`, `MONTHLY_RANKING` and `DIVISION_DASHBOARD` read rows 38 and 44,
not row 15 directly, and all three recalculated correctly.

Also corrected across all sheets: three remaining "Kailash Babu" labels, the
POINTS SYSTEM banner (now states both the pre- and post-Aug-26 rules), and the
SECTION A caption, which explains that rows 1–6 are the old type list so the
TOTAL row can legitimately exceed the sum of the six rows above it.

### The LC No. column — gates are now stated, not guessed

INSPECTIONS has a new **LC No.** column (column I; Remarks moved to J). When it
holds a value the dashboard uses it and does not read the prose at all.

- `16, 22, 25, 30` — four gates, exactly.
- `16 (ROK-GHNA)` — add the block section in brackets when a number repeats.
  TI/PNP has three different gates numbered 16 on three sections; without the
  section no parser, and no person, can tell which one was inspected.
- A number that matches no gate on that TI now matches **nothing**. It is not
  quietly rounded to the nearest station's gate — a wrong entry stays visible
  as a gate that was never inspected.

Leaving the cell empty keeps the old behaviour, so no historical row changed.

Verified end to end: an explicit list resolves to exactly the gates named, a
section in brackets separates same-numbered gates, a bad number matches nothing,
and a blank cell still falls through to the remark parser.

`KPI_SCORES` was byte-identical before and after the column insert — the tracker
`COUNTIFS` all reference columns D, E, G and H, which sit left of the insert.

### Map legibility when zoomed

Three separate faults were making gates disappear on a zoomed map. All are fixed.

1. **The label halos were sized in user units.** `stroke-width` on the label
   text was a fixed length, so as the viewBox shrank the halo grew on screen —
   by 30x it was a dark slab wide enough to swallow the station dot and every
   gate mark beside it. Halo widths are now CSS variables recomputed per zoom,
   exactly as the type size already was. This was the worst of the three.
2. **Off-screen labels were competing for space.** The declutter pass measured
   all 500-plus labels, including the ones nowhere near the viewport, so gates
   the user was looking at lost to gates they could not see. It now skips
   anything outside the viewport.
3. **A clashing label was dropped outright.** It now tries eleven nearby
   positions before giving up, and gate labels outrank the minor station names
   once past 2.6x — zoomed in, the gates are the subject of the map.

Engineering gate numbers also appear from 2.0x instead of 3.6x, traffic numbers
from 1.15x instead of 1.7x, and **Show all names** now reveals gate numbers too.

Measured across 1x, 2x, 4x, 8x, 16x and 32x: every gate on screen keeps its
label at every level, with **zero overlapping label pairs**. Before the change,
2x showed no engineering label at all and 4x dropped 95 of them. The pass costs
about 8 ms.

### Map legend

The legend is an overlay, and on a smaller map panel it was covering the left
of the map and sliding under the toolbar once that wrapped to two rows. It now
collapses to a single **LEGEND / Show** chip, and:

- opens by default only when the panel is at least 975 px wide — the point at
  which the 214 px key stops being more than about a fifth of the map;
- takes its ceiling from the toolbar's measured height, so a wrapped toolbar
  can never overlap it;
- scrolls inside itself when the panel is short;
- keeps whatever the reader chose, through metric, gate-filter and theme
  changes.

Also removed: the "*n*% of this total is bonus filings rather than field
inspection" note on the scorecards, and the calculation behind it.

### Still worth doing

- **Rows 1–6 of SECTION A still show only the six old types.** The totals are
  right, and the SECTION A caption now says so, but a gate or footplate
  inspection is not visible as its own line. Adding those rows means inserting
  rows on 13 live scoring tabs and rebuilding the `Q` and `R` columns — worth
  doing calmly, as its own task.
- **The Time column is still empty.** I left it that way on purpose: writing
  "Night" into the 96 night-named rows would change no number, because a blank
  Time cell already falls back to the type name in both the dashboard and the
  tracker. It was not worth a write against live data for zero effect. Fill it
  going forward and the fallback stops mattering.

---

## Part 3 — What changed in this build

- **160 stations everywhere.** `DSA(A&B)` is split into `DSA(A)` and `DSA(B)`
  as separate panels, and the station count is derived from the coordinate
  table rather than typed in. Old sheet rows reading `DSA(A&B)` are aliased to
  `DSA(A)` so nothing already logged is lost.
- **TI/DEC is Shankar Lal Meena** in the dashboard and in the sheet, with no
  date condition left anywhere.
- **Gate scheme live from Aug-26** (`SCHEME_CUTOVER`), not September.
- **Traffic gates are drawn as arrows.** Each TLC now has a dot on its own
  station, a shaft, and an arrowhead at the gate chip, all in the gate's
  inspection colour — so which station a gate belongs to is unambiguous.
- **TI cards now carry gates.** Every TI card has three groups: Stations,
  Traffic gates (TLC), and Engineering non-interlocked gates (ENLC).
  Interlocked gates are excluded and counted in a note, because they are never
  inspected.
- **ENLC tiles fill in quarters.** Each inspection of a non-interlocked gate
  fills 25% of its tile green from the bottom; four inspections — one a week —
  make one whole gate. The card header sums this as "x of n gates earned".
- **Map gate visibility.** A new control next to the map measure gives *All
  level-crossing gates*, *Traffic gates only* (engineering gates hidden), or
  *No gates* — the legend follows the choice.
- **Gate-number matching rebuilt against your real rows.** The remark parser used to swallow
  everything up to the next full stop after the first keyword, so a row reading
  "Gate Inspection … LC No. 34" matched no gate at all and scored no gate
  credit. It now reads the number list behind each keyword without consuming
  the text after it. Three further faults found by testing against the ten real
  level-crossing rows in your sheet:
  - The inspection type "Night Inspection - Level Crossing" normalises to "…LC"
    and was swallowing the "1." that opens nearly every remark, so most rows
    matched a phantom **LC 1** instead of the gate inspected. The type is no
    longer used for number extraction.
  - The trailing letter in "LC 22/C", "LC-12C", "205 SPL" is the gate's
    **class**, not part of its number. An exact workbook number still wins
    (4C, 67A and 32B are real LC numbers), and the class is stripped only when
    no gate carries that literal number.
  - The numbered deficiency list in the remarks is no longer read as gate
    numbers.

  Before: `LC-12C` matched LC 1, `Lxing no.16C, 22C, 25C, 30C` matched LC 1,
  and `LC 4 SPL TPZ` matched nothing. After: LC 12; LC 16, 22, 25 and 30; LC 4.

### Known limit — gate identity comes from free text

`Lxing no.16C ,22C,25C,30C` on TI/PNP still resolves to seven gates, not four,
because PNP has three different gates numbered 16 on three different block
sections and the row gives no section. No parser can separate those. If gate
scoring is going to carry weight, add an **LC No.** column to INSPECTIONS and
let the gate be picked rather than typed into prose — that removes the guessing
entirely.
