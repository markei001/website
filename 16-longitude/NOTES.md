# 16-longitude: "The Slow Light"

A long-form essay (about 1,800 words of body text, plus eight margin notes and six endnotes) on why starlight is old. One typeface family for the text (Literata), one small sans for UI (Hanken Grotesk), no images beyond five tiny inline-SVG margin diagrams. Accent `#963a1a` (night: `#e8a176`).

Files: `index.html` plus `fonts/Literata.woff2` and `fonts/Literata-Italic.woff2` (subset, variable `opsz` + `wght`).

## Pass 1: correctness

Verified by driving the page with puppeteer-core in Brave (Chromium 157, headless) and in Firefox 160 (WebDriver BiDi).

- **Console:** zero errors or warnings in both engines at 1440, 1180, 1024, 768, 390 px. The first run in Chromium *did* log one: I had a `<link rel="preload" ... crossorigin>` for the font, and Chromium refuses CORS font preloads from `file://`. Removed the preload; the `@font-face` itself loads fine (checked `document.fonts`: Literata roman and italic `loaded`).
- **Fonts:** Google's CDN strips `onum`, `smcp`, `c2sc` from every serif I tested (Newsreader, EB Garamond, Source Serif 4, Literata, Crimson Pro, Spectral: all return only liga/pnum/tnum/frac). The full files in the `google/fonts` repo have them. So Literata is subset with fontTools (Latin + punctuation, layout features kept, woff2) and hosted in `fonts/`. Real old-style figures and real small caps, not synthesized ones.
- **390 px:** `scrollWidth == clientWidth` (no horizontal scroll). Header (contents, lookback, time left, theme) fits. Tapping a margin-note number opens it inline with its diagram (checked note 4). Contents panel opens and closes on link click and on Esc.
- **Layout bands:** 3-column (contents / text / margin) from 1180 px; below that a single column with tap-to-open notes. At 1180 the margin notes stay inside the viewport.
- **prefers-reduced-motion:** emulated; `scroll-behavior` computes to `auto`, transitions and animation are off. The progress line and lookback counter are scroll-driven values, not animations.
- **No JS:** the essay, drop cap, notes (checkbox-driven), and the light/dark system theme all work; JS-only controls are hidden; a `<noscript>` note says what is missing.
- **Contrast (WCAG, computed):**
  - light: ink 13.4:1, muted 6.2:1, accent 6.3:1 on paper; muted 5.6:1 on the darker paper-2.
  - dark: ink 12.7:1, muted 7.0:1, accent 8.8:1.
  - worst case anywhere, text on the hover/anchor highlight tint: 4.8:1 (muted, night). Lowest accent-on-highlight: 5.0:1 (day).
- Theme choice persists in `localStorage` (wrapped in try/catch), and is applied before first paint.

## Pass 2: intent beyond defaults

- **Chapters are distances.** The structure is the argument: Moon (1.3 s), Sun (8 min 19 s), neighbours (4.2 yr), Andromeda (2.5 Myr), the early universe (13.5 Gyr), then back to now.
- **Lookback counter.** The header shows how old the light you are reading about is, interpolated on a log scale as you scroll between the chapter figures, and heading back to "now" in the last section. The log-scale ladder diagram in the final margin note explains why it speeds up then stalls.
- **"Time left" is measured, not estimated from scroll %:** it sums the words of paragraphs below a reading line 40% down the viewport, at 230 wpm.
- **Night reading is a type decision, not an inversion:** weight 400 to 350, tracking +0.009em, leading 1.62 to 1.74, warm grey ink, accent lightened. Headings drop weight too.
- **Typography:** `opsz` axis driven by `font-optical-sizing` (display cut in the h1, text cut in the body); small-caps lead-ins on every chapter; drop cap that upgrades to `initial-letter` where supported (Chromium) and falls back to a tuned float (Firefox); hanging opening quote on "Light-year" (manual, since `hanging-punctuation` is Safari-only); old-style figures in text, lining tabular figures in UI; `text-wrap: pretty/balance`; hyphenation limited so short words never break; narrow no-break spaces in numbers and units.
- **Notes:** margin notes on desktop, inline toggles on small screens; hovering a note lights its anchor and vice versa. Endnotes: hovering, focusing or jumping to one lights the anchor in the text; "Back to text" flashes it.
- Five diagrams drawn for the content rather than decoration: parallax, Andromeda as six Moons, redshifted hydrogen lines, the lookback ladder (all use `currentColor`/tokens so they follow the theme).
- Barely-there paper grain (SVG noise at 5% day / 2% night).

## Pass 3: stranger test ("made, not generated")

- Cut the generic. Every claim in the prose is a specific number, name or date, and several carry honest hedges ("somewhere between 550 and 700 light-years, depending on whose measurement you prefer"). One notes that the Sun-photon estimates range from ten thousand to over a hundred thousand years.
- The essay corrects a popular half-truth (that you are mostly looking at dead stars) instead of repeating it.
- First-person voice appears twice and only where an author would ("as I write", "I find this steadying"). Jokes are dry and small (the lantern experiment measured the assistant; a satellite "will not be interested").
- Trimmed about 330 words from the first draft so the page does not say "9 minutes". Several items moved into margin notes instead of being deleted.
- Fixed things a stranger would notice: footnote markers that left a gap before punctuation, notes opening mid-sentence with a stray comma (markers now follow punctuation), hyphenated short words ("pho-ton"), a lookback figure that was off by a factor of ten (Andromeda read "25 million years" until the test showed it), and 8 min 20 s changed to 8 min 19 s to match the prose.
- Colophon says plainly what was set in what, and why the font is self-hosted.

## Facts to re-check if you edit the prose
Light-travel figures, Rømer 1676 / 22 min estimate, Bessel 1838 (about 10.4 ly vs 11.4 now), Leavitt 1912, Hubble 1923 plate (1925 paper; first distance about a third of today's), JWST z > 14 (Carniani et al. 2024), CMB at about 380,000 years, 2.7 K, factor about 1,100, Penrose's "several days".
