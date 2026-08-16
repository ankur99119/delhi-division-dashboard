# Delhi Division TI Dashboard v2

GitHub Pages-ready dashboard for Sectional Traffic Inspector performance in Northern Railway's Delhi Division.

## What is included

- One unified geographic division map. Delhi-area detail appears on the same map at closer zoom.
- All 160 Delhi Division stations are represented, with `DSA(A)` and `DSA(B)` as separate panels, NOLI shown only by its official code `NO`, and the Delhi-area network merged into the same map. Rows already logged as `DSA(A&B)` are aliased to `DSA(A)`.
- Traffic gates are drawn as an arrow from their own station to the gate chip, in the gate's inspection colour.
- Each TI card lists that TI's stations, traffic gates (TLC), and engineering non-interlocked gates (ENLC). ENLC tiles fill green a quarter at a time — four inspections, one a week, make one whole gate. Interlocked gates are excluded and shown only as a count, because they are never inspected.
- TI/DEC is Shankar Lal Meena. The gate/day-night scoring scheme is live from Aug-26.
- An optional **LC No.** column in the sheet names the gate directly. When present it overrides the remark parser entirely, and a section in brackets — `16 (ROK-GHNA)` — separates gates that share a number on different block sections.
- All 359 supplied level-crossing gates are integrated: 120 Traffic gates at stations and 239 Engineering gates on their block sections. Each popup retains its workbook identity, LC number, location, class, interlocking, barrier, TVU, census, TI HQ, and source serial.
- Map labels: `TLC` Traffic, `ENLC` Engineering non-interlocked, `EILC` Engineering interlocked, each with its L.C. number.
- Only the 120 Traffic and 131 Engineering non-interlocked gates are inspected, so only those 251 carry inspection-recency colour. The 108 Engineering interlocked gates are never inspected and hold one fixed reference colour (#8b5cf6, dashed bar) in every metric.
- Gate type is carried by shape as well as colour — round head for Traffic, single bar for non-interlocked, double bar for interlocked — so type survives in greyscale and under colour blindness.
- Map labels are decluttered by a greedy collision pass after each render: stations win over traffic gates, which win over engineering gates. Suppressed names remain in the popups and data tables.
- Twenty user-verified operating sections display literal single, double, four-line, or six-line track configurations; unclassified tracks remain as subdued geographic context. This includes the double line from PTNR to RE via Delhi Cantt, Gurgaon, Garhi Harsaru and Patli.
- MNAR is placed on the Patli–Manesar alignment, and its single-line connection originates at Patli.
- Bundled state areas, rivers, settlements, graticule, and railway context—no external map tiles.
- Pan, zoom, full-screen map view, Delhi fly-to, station popups, TI focus, inspection heat layers, and light/dark themes.
- Level-crossing gates can be shown in full, restricted to traffic gates, or hidden entirely.
- Automatic block sections are drawn in electric green beneath the track lines, sliced from the real alignment, so signalling territory and track count read at the same time.
- Label halos are part-transparent, so a name never blanks the rail, river or gate behind it.
- A **Last inspected** lookup searches every station and gate by code, number, TI, section or location and returns the exact date and the gap in days. Exact codes rank first.
- Stations over 60 days and 31-60 days pulse in the recency view — a staggered, slow ring that stops under `prefers-reduced-motion` and never runs in the count or signalling views.
- The map legend collapses to a single chip. It opens by default only when the map panel is wide enough that the key stays under about a fifth of its width, is bounded by the toolbar so it can never slide underneath, scrolls when the panel is short, and remembers the reader's choice.
- Label placement keeps every on-screen gate labelled at every zoom level with no overlaps: halo widths scale with the viewBox, off-screen labels are skipped, and a clashing label is nudged to one of eleven nearby positions before it is dropped.
- Operational context: automatic/absolute block display, Yamuna/Hindon/Ghaggar references, and distinct WDFC, RRTS, and HORC overlays. New Prithla Jn is plotted west of the Delhi–Mathura line, with separate WDFC connections to AST and PWL forming the supplied map's Y geometry.
- Both supplied PDF pages are retained only as source drawings in the map's **Source drawing** viewer; they are not separate dashboard maps.
- Live Google Sheets loading with timeout, automatic validation, and partial-cache fallback.
- Shared DLI/DELHI ownership is preserved instead of silently assigning all shared stations to one TI.
- Division summary counts each physical station once, and the station count is derived from the data rather than hardcoded.
- The counselling headline states how many rows carry no valid TI code, so the division total and the per-TI boards reconcile.
- Where the zero floor masks a negative net score, the card shows the true unfloored value.
- Zoom is clamped to 1x-60x and the +/- buttons aim at the stations in view.
- Incident deductions prefer the sheet's TI Section and only infer ownership when a station has one unambiguous owner.

## Deploy to GitHub Pages

Copy `index.html`, `gate-data.js` (this exact hyphenated filename — the page loads it by name), and the `assets/` directory to the repository root, commit, push, and enable GitHub Pages for that branch/folder. No build step is required.

The map, controls, source drawings, and saved data work without an external map service. Internet access is needed only to synchronise new records from the public Google Sheet. Direct `file://` opening uses Google Visualization's script-response mode; GitHub Pages uses fetch with the same script mode as fallback.

## Source and safety notes

- Operational source drawing: Northern Railway Delhi Division System Map, corrected to 31 March 2024, marked “not to scale”.
- Level-crossing source: `TOTAL L-XING GATES DLI DIVN.xls`, reconciled at 359 rows as at 14 August 2026 (120 Traffic and 239 Engineering).
- The gate workbook has block section and chainage/location text but no latitude/longitude columns. Traffic gates are therefore anchored to their named station; Engineering gates are ordered by workbook chainage and placed along the matching surveyed railway route between the two named section endpoints. Field-verify an individual gate coordinate before safety-critical use.
- Geographic context and simplified track/river geometry are bundled in the HTML. No third-party map tile server is required. Track and river data are © OpenStreetMap contributors under the ODbL. State outlines are from geoBoundaries / DataMeet India / Election Commission of India under CC BY 2.5 IN.
- 146 station codes were matched directly to railway station/halt references on 15 August 2026. The remaining 13 operational points were aligned to verified track geometry and the supplied division drawings; MNAR uses the mapped Patli–Manesar construction alignment. No marker remains visibly off its railway line. Field-verify all coordinates and track counts before safety-critical use.
- Location fields such as level crossings, route spans, footplate runs, and free text are excluded from station coverage rather than treated as master stations.
- Google Photorealistic 3D is intentionally not a dependency because reliability from a local file and GitHub Pages is more important than an API-key-dependent basemap.
