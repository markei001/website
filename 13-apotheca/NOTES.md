# 13 APOTHECA — build notes

Single file, `index.html`. Hanken Grotesk (variable 100–900) + DM Mono from Google Fonts. No libraries.
Paper #F5F4EF, ink #111111, one signal #C93408. Everything is plain DOM + inline SVG.

## Pass 1 — correctness
Verified in headless Brave (Chromium 157) over CDP, not just by reading the code.
- Console: zero errors or warnings on load, on all 30 plate renders, on filter, sort and reset.
- 390 px and 320 px: no horizontal overflow (checked every element's right edge).
  This found one real bug: the Holdings stat block (`Entered 1958–2021`) pushed the page to 507 px.
  Restructured into three label/value cells that grid-wrap on mobile.
  The giant masthead was re-measured and set to 19.2vw (desktop) / 18vw (mobile) so it fills the measure at weight 900.
- Table reflows below 760 px to a 3-row card per compound (number | name + mass | class + status | formula).
  Header buttons disappear, so a Sort select and direction button replace them. Plate sits above the table under 1100 px.
- `prefers-reduced-motion: reduce` (emulated): all `[data-sd]` animation is removed, masthead stays at 900,
  statement stays full ink, page counter is hidden. Confirmed by computed style at four scroll positions.
- Fallback path (animation-timeline forced off): same keyframes run paused with a negative `animation-delay`
  set from scroll position (IntersectionObserver limits work to visible nodes). Confirmed that masthead weight,
  numeral weight, word colour and the page counter all track scroll.
- Contrast: ink on paper 17.1:1, signal on paper 4.79:1, signal/white 5.28:1, muted grey #5f5d56 5.98:1.
  The only sub-4.5 colour is the "ghost" start state of the statement words, which is mid-animation and ends at ink.
  Dim register cells (#a29f94) are disabled-state UI.
- Also: `<title>`, meta description, theme-color, inline-SVG favicon, `<noscript>`, `lang`, focus-visible rings,
  aria-sort on headers, aria-pressed on chips, aria-live result count, role=img + label on each plate.

## Pass 2 — intent beyond defaults
- Data is internally honest: molecular mass is computed from the formula with real atomic weights;
  the plate SVG is generated from the same formula's N/O/S/halogen counts (seeded per compound) so the drawing and the table agree.
  Red marks every non-carbon, non-hydrogen atom. Salts get floating ions.
- The only motion is type: masthead weight 900 to 150, section numerals 100 to 900, statement words ghost to ink,
  and a page-percent counter driven by an animated `@property` integer, all on scroll timelines.
- Press **G** for a live 12-column overlay (the layout really is on it: text sits on column starts).
  `/` focuses search. Register strip of 30 positions mirrors the filter state.
- Detail work: tabular numerals everywhere, subscripted formulae, status glyph legend (filled / hollow x ink / signal),
  selected row inverts to ink, Latin and German micro-labels, crop marks on the masthead, scale bar and corner ticks on plates,
  an errata line, hairlines at exactly 1 px, text-wrap pretty on running text.

## Pass 3 — stranger test
Round one: plate renders had peroxide chains (O-O-OH) and unlabelled halogens that read as generated nonsense.
Fixed by forbidding hetero atoms beside other hetero atoms and adding substituent capacity.
The footer began as a generic "A-Z" and became "Nº 13" to match the catalogue number.

Round two, from a review of the screenshots (all real finds, all fixed):
- The red word "fictional" in the Statement was turning black: the per-word ink animation overrode the `<em>` colour.
  Added an `ink-sig` keyframe for words inside `em`; computed colour checked after scrolling past.
- Holdings copy claimed the figures move with the filters. They do not, so the copy now says they are computed from the data.
- The sticky plate was taller than a 1440x900 viewport and its lower rows were cut off. Compacted (capped SVG height,
  smaller numeral and heading, tighter rows; caption hidden on short screens). It now measures 798 px against 840 px available at 1440x900.
- Two big numerals (result count, mean mass) were synthesised-bold DM Mono. Both set in Hanken Grotesk.
- Register cells now keep keyboard focus after a select; page counter widened so "100%" does not touch the label.
- Tablet widths 768 and 1024 checked: no horizontal overflow. Below 1100 px the plate stacks above the table.
Known limits: plates are schematic and say so on the figure; at 1024 the stacked plate is tall.
