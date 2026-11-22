# RESONANCE: build notes

Single file, `index.html`, about 45 KB. No libraries, no audio files. Fonts: Bodoni Moda (display, italic) and Fragment Mono (everything else).
Accent `#c8ff5a` (phosphor lime); the spectral ramp runs rose, orange, lime, mint, blue, violet.

## Pass 1: correctness
Checked in headless Brave 157 over CDP (desktop 1440x900 and mobile 390x844).
- Console: zero errors or warnings across load, scrolling all five rooms, Enter with sound, mic on/off, pointer plucks.
- Synth verified for real: after Enter the AudioContext is `running`, 6 voices are alive (5 pad notes + bass) and the AnalyserNode reports non-zero spectrum data. Source label flips SIMULATED -> SYNTH -> MIC -> SYNTH as expected.
- 390px: no horizontal overflow; rooms II-V restack to canvas-then-plate; HUD becomes a compact bottom bar; the room rail clears the copy (50px right padding).
- `prefers-reduced-motion`: verified with emulation. Draw loop drops to about 4 fps with fixed small time steps, trails become full clears, hero letter wobble, ring ripples and bell bursts are off, smooth scrolling is off. The page still shows a live (slow) image rather than a blank one.
- Mic permission denied: verified by forcing `NotAllowedError`. Toggle stays off, `aria-pressed` stays false, a polite live-region message explains, the simulation keeps running. NotFound / unsupported / insecure cases have their own messages.
- Contrast on `#07060a`: body ink 16.8:1, soft text 12.8:1, muted 8.3:1, accent 17.2:1, orange mic dot 8.9:1; button label on lime 17.2:1. Plates and HUD sit on opaque-ish dark panels so text never sits on moving colour.
- Fixed while testing: `AudioContext.resume()` is no longer awaited before `getUserMedia` (it can hang without a gesture); bloom pool saturated (1450 live grains) so emission was halved; hero title overlapped the halo so both were resized.

## Pass 2: intent beyond defaults
- **One score, two outputs.** A small chord scheduler (six chords, shared notes kept so voices lead) drives both the real synth and a simulated spectrum. Muted mode is therefore not a loop or random noise: it draws the same harmonics, the same bell echoes (0.43 s delay taps) and the same filter breathing the ears would get. When sound is switched on the visuals cross-fade from simulation to analyser over about two seconds, so there is no dip.
- **Honest instrument labelling.** Wall labels carry band and method; each plate has a live readout (loudest pitch as note name and Hz, grain count, Lissajous lag in samples, chord clock). The HUD names the current and next chord.
- **Playable.** Click or tap any room to pluck a note; x chooses the degree of D dorian (so it is never wrong), y chooses velocity. It sounds if sound is on, and in silence it still blooms and rings in the note's own spectral colour.
- **Rooms change the synth.** The active room sets pad cutoff, reverb and delay feedback and bell rate (Ridgeline is dark and slow, Bloom bright and busy, Figure dry so the wave stays legible).
- **Mic is a mode, not a overlay.** The synth ducks while the mic is live to avoid feedback, and gain is normalised per source so a quiet mic still draws.
- Details: the title's nine letters each ride a frequency band (scaleY and a glow in that band's colour); the Halo wheel has frequency labels inside it; Sediment drops chord names into the time lane, with dotted pitch rulers; the Figure colour follows the loudest pitch.

## Pass 3: stranger test
Looked at it as someone who has never seen the brief.
- Removed anything that reads as template: no hero badge row, no feature cards, no gradient-text headline, no generic "scroll to explore". Headline is plain white Bodoni with a glow that only appears where the sound is.
- Copy is specific and slightly odd on purpose: Harold Craft's 1970 pulsar plot, "leave a line in the silt", the chord names, the signal path laid out as a literal chain.
- Plates are written like museum captions (number, year, medium, band) instead of feature blurbs; the type pairing is a high-contrast didone against a monospaced instrument face.
- Honest limits kept visible rather than hidden: the Figure room is a tangle when five voices sound (that is what the maths does); the footer says the sim is a computed spectrum, not a recording.
- Known soft spots: peak-pitch readout can lock on a sub-harmonic of the bass in the real synth; no true WebGL (all Canvas 2D); Firefox and Safari were not tested.
