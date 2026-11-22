# CLAY - build notes

Single file, no build. Three.js 0.160.0 via ESM import map (jsdelivr). Fonts: Young Serif + Familjen Grotesk.
One WebGL renderer draws every vessel (hero, 8 shelf cards, the wheel) into its own 2D canvas, so the page uses one GL context.

## Pass 1 - correctness
- Headless Chromium (Playwright, SwiftShader GL): zero console errors/warnings at 1440x900 and 390x844; no horizontal scroll at 390 (scrollWidth 390).
- Tested interactions: hero drag reshapes (Ø 15.1 -> 20.0 cm), pad keyboard arrows change volume/price, crate add/total/Escape.
- prefers-reduced-motion: no turntable, no reveal transitions, no smoothing; repaint only when geometry changes. Tested with emulated reduce.
- WebGL unavailable (and webglcontextlost): same slots fall back to 2D canvas silhouettes drawn from the same profile data. Tested with --disable-3d-apis. Hand/pad/readouts still work.
- Contrast (body/bone): ink 14:1, muted #6B5D50 5.4:1 (4.7:1 on the darker panel), terracotta text #A3431F 5.3:1 (the lighter #C2603A is decorative only), cobalt #2B3BE8 6.1:1, cobalt-lt #9AA6FF on ink 7.4:1, bone on terracotta footer 5.3:1.
- title, meta description, inline-SVG favicon, noscript message, aria labels on canvases, pad handles are focusable role=slider with arrow keys, visible focus ring, Escape closes crate.

## Pass 2 - intent beyond defaults
- The seed is a "firing number" kept in ?f=NNNN: reload = new kiln load, copy the URL = same shelf. A stamp, the nav copy and the footer all carry it.
- Prices, capacity (ml) and weight (g) are computed by integrating the actual inner/outer profile, not invented. They move live as you reshape.
- Real pottery details in the shader: waxed bare foot with wavy resist line, glaze pooling dark at the base, drip lines under the dip, darker band where glazes overlap, iron specks, glaze breaking to clay on the rim, glossy vs raw roughness, throwing rings and a slight hand wobble in the geometry itself (not a bump map).
- Hero is pressed like clay: hover leans, press kneads and is kept; shelf cards lean and spring back.
- The "rib": every profile can be exported as a mm-accurate SVG cut file; the Method section draws the hero's rib live.
- One loud glaze (Cobalt Slip) is the only saturated colour, guaranteed to appear on the shelf.

## Pass 3 - stranger test (made, not generated)
- Avoided: gradient hero, glass cards, centred stack of three feature cards, stock testimonial row, emoji icons.
- Uneven shelf (alternate columns dropped), shelf slab under each pot, impressed stamp rotated -11deg, ledger-style "No." codes and spec rows, a taped-on cobalt caption tilted 2.5deg, paper grain over everything.
- Copy is specific (cone 6 / 1222 C, 3 mm birch, collection Thursdays) and says plainly it is a demo shop.
- Thumb-print cursor ring over clay areas (fine pointers only).
- Cut: a spinning hero headline effect, parallax, and a fake testimonial strip.

## Bug found during pass 1
Hero/card reshaping updated the readout but did not repaint under reduced motion or in the 2D fallback (draw was gated on `dirty` only). Fixed by gating on `rebuild||dirty`; re-verified with screenshots in both modes.
