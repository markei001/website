# 15-riso / PRESS GANG: build notes

Single file, no build, ~70KB. Fonts: Bagel Fat One (display) + Special Elite (typewriter body), both from Google Fonts. No JS libraries.

## Technique
- Ink layers are separate stacked elements with `mix-blend-mode:multiply` on a cream stock, so overlaps genuinely multiply (pink x yellow = orange, pink x blue = violet).
- Halftone: a tiny JS generator emits one `<path>` of circles per screen on a rotated grid (pink 15deg, blue 75deg), with dot radius from a coverage function. Moire appears when layers drift.
- Grain: static `feTurbulence` rasterised once into data-URI tiles, used as a CSS mask (ink dropout) and as a paper-tooth overlay. `feDisplacementMap` (`#rough`) wobbles ink edges, applied only to hero art, hero/logo headlines and flyer layers.
- Misregistration: global `--jx/--jy` updated from scroll position, scroll velocity and pointer position; each ink moves in a different direction. `--m` (registered `@property`) multiplies it, so hover/focus on a card or headline entering the viewport animates the drift in and out. Header toggle: tight / loose / wild.
- Torn paper: seeded random `clip-path: polygon()` on the section edges. Tape is clip-pathed zigzag ends. Stickers drag.
- Zine: real 3D page-turn (leaf `rotateY` about the spine, backface hidden), two DOM modes (spread on desktop, single page <=760px), keys / buttons / swipe / tap edge, contents list jumps to a page.
- Flyer tool: two ink layers you can drag (or arrow keys, shift for bigger steps), 9 inks, 3 papers (kraft eats yellow, as in life), live mm-off-register readout with a grade, "pull a print" wipes the two passes in sequence.

## Pass 1: correctness
- Checked in headless Brave (Chromium 157) over CDP: zero console errors/warnings/exceptions at 1440x900, 390x844 (mobile emulation) and with `prefers-reduced-motion: reduce`. Exercised: zine flipping (desktop + single-page mode), flyer keyboard nudge / snap / worse / pull-a-print / art + paper + ink switching, form submit, click-through to the back cover on mobile (Turn disables at the back cover).
- Found and fixed: hero headline jitter was proportionally huge on phones (now scaled by viewport width); footer wordmark and a hidden stamp caused horizontal overflow at 390px (scrollWidth is now 390); single-page book kept a two-page width; small hint text on teal was 4.3:1 (moved onto a paper chip).
- Second sweep on the zine: contents-list jumps (e.g. to the back cover) used to stall because the retry counter burned ticks while a page was mid-turn; fixed and verified at 1440 and 390 (jump to back cover, jump back, then Next x10 stops on the back cover with Turn disabled; single-page mode cannot turn past the last page). Form success now moves focus to a `role=status` message.
- Reduced motion: marquee, land-in animations, spin, page-turn shade, `--m` transitions and "pull a print" are all off; scroll-linked jitter is replaced by one static offset.
- Contrast: body navy `#15184f` on cream `#f3ead2` ~14:1; navy on pink `#ff48b0` 5.3:1; navy on yellow ~13:1; cream on the darkened flood blue `#00649f` 5.4:1; muted `#42446a` on cream 7.7:1. Fluorescent pink and plain blue are only used for display type (>=24px) or fills, never for body copy.
- Performance: heavy things (turbulence/displacement) run on static content; layers that move are their own composited elements (`will-change:transform`) rather than SVG children, so filters don't re-run on scroll. The jittering `rough2` headline spans (hero, logo, footer) get `will-change:transform` for the same reason; other headings use only the cheap mask grain. Zine pages and avatars only drift on hover, not scroll. `<noscript>` fallback has the essentials.

## Pass 2: intent beyond defaults
- Registration is a user setting, not just decoration (the tight/loose/wild switch also serves people who find the drift too much).
- Real print furniture: corner registration crosshairs (two inks, misaligned), colour bar, edition numbering (copy no. 088 of 150), crop-like corner marks on the flyer, kraft stock behaving correctly under multiply.
- Section "floods" map to inks: pink (the gang), blue (the press), yellow (print nights), teal cutting-mat (the zine desk), navy footer where the headline switches to `screen` blending because overprinting on dark paper would just go black.
- The flyer tool teaches the thing the site is about: you can see the mm and the grade as you break registration.
- Headings land in register as they scroll in.

## Pass 3: stranger test
- Cut anything that read as template: no icon rows, no three identical feature cards, no "Welcome to".
- Specific, slightly dumb voice: a duplicator named Brenda, errata for issue #6 ("do not eat the toner"), a strike-through "elegant" panic edition, a lost yellow drum called Sunny, "Lift: broken. Stairs: honest."
- Off-grid layout: cards at different rotations and offsets, tilted sticky note, staples of tape, a coffee-ring stain in the footer, a band that is itself torn and tilted.
- Dates are real for the site's "now" (22 Nov 2026): Tue 24 Nov, Tue 1 Dec, Fri 4 Dec, Sat 5 Dec, Tue 15 Dec.
- Everything is authored: SVG shapes, generated halftone, no stock imagery or emoji.
- Known limits: the form is client-side only (shows a stamp, sends nothing); the flyer can't be exported as an image.
