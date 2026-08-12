# Design and Brand Spec

The brief's hardest requirement: "unmistakably this event, not a generic badge with a
logo pasted on." Everything below serves that line.

## Concept

Goan sunset meets hacker terminal. Portuguese-Goan azulejo tilework and beach-shack
signage, rendered with the grain, scanlines and monospace data fields of a hardware
badge. Warm, sun-bleached, slightly analogue - not another dark-mode neon gradient.

Three motifs repeat across every asset:
1. The sun - a halftone/scanline disc, the hero motif
2. The wave - layered sine bands used as dividers and ring texture
3. The tile - azulejo diamond pattern as a low-opacity background texture

## Palette

| Token | Hex | Use |
|-------|-----|-----|
| sunset | #FF6B35 | primary, rings, headlines, CTA |
| magenta | #E63B7A | gradient partner to sunset |
| indigo | #1B1B3A | deep background, card body |
| ocean | #0E7C86 | secondary accent, wave bands |
| sand | #F5E6D3 | card surface, body text on dark |
| lime | #C6F135 | highlight chips, "2026", live dot |
| ink | #111018 | text on light surfaces |

Variants:
- sunset:   #FF6B35 -> #E63B7A -> #1B1B3A
- ocean:    #38E5C8 -> #0E7C86 -> #10233F
- coconut:  #F5E6D3 -> #E8B23A -> #6B3F1D
- midnight: #7B5BFF -> #1B1B3A -> #05050C

## Typography

| Role | Face | Treatment |
|------|------|-----------|
| Display / name | Clash Display Bold | tight tracking, optical caps |
| UI / labels | Space Grotesk Medium | - |
| Data fields, ID no. | JetBrains Mono | 0.08em tracking, uppercase |

Self hosted woff2, latin subset. Names auto-fit: font size steps down until the string
fits its box, so long names never overflow or clip.

## Format A - PFP Frame

Canvas 1600x1600. X crops profile pictures to a circle, so all art lives inside the
inscribed circle.

    +--------------------------+
    |      .------------.      |  outer ring  r = 800 -> 720
    |    /   ARC TEXT     \    |  "HACK HOUSE GOA . 2026 ."
    |   |   +----------+   |   |  inner ring  r = 720 -> 690
    |   |   |  PHOTO   |   |   |  photo circle r = 690, cover fit
    |   |   |  circle  |   |   |
    |    \  +----------+  /    |
    |      '--[BUILDER]--'     |  bottom chip, lime on indigo
    +--------------------------+

Layers, bottom to top:
1. Transparent background so it works on any X theme
2. Photo, circular clipped, cover fit with user transform
3. Inner 1px sand hairline
4. Ring band: variant gradient + azulejo tile at 12% + wave stripe
5. Arc text along the ring, sand, mono, 0.14em tracking
6. Sun motif at 12 o'clock, palm fronds at 4 and 8 o'clock
7. Bottom chip: BUILDER / HH GOA '26
8. Global grain overlay at 6%

Composition rule: the photo occupies 86% of the diameter. The frame wraps, never competes.

## Format B - Builder ID Card

Canvas 1600x2000, plus a 1600x900 landscape variant for better X inline display.

    +--------------------------------+
    |        o  lanyard hole         |
    +--------------------------------+
    | HACK HOUSE GOA        * LIVE   |  header bar, mono
    | 2026 . BUILDER PASS            |
    +--------------------------------+
    |   +------------------+         |
    |   |      PHOTO       |         |  4:5 rounded slot, cover fit
    |   +------------------+         |
    |                                |
    |   SHIKHAR SRIVASTAVA           |  Clash Display, auto fit
    |   @handle                      |  lime mono
    |                                |
    |   [ BEACHSIDE BACKEND BANDIT ] |  generated title, gradient plate
    |                                |
    |   STACK      Full-stack / Rust |  mono data rows
    |   ID         HHG-2026-0428     |
    |   ISSUED     GOA, IN           |
    +- - - - - - - - - - - - - - - - +  dotted tear line
    |  [QR]  #FrameInGoa             |  QR to x.com/<handle>
    +--------------------------------+

Details that sell it as a real artifact:
- Lanyard hole and faint strap shadow at the top edge
- Dotted perforation line above the footer
- Holographic sheen: soft diagonal white gradient at 8% across the card
- Azulejo tile watermark at 6% behind the data rows
- Grain overlay at 5%
- Corner registration crop marks in sand at 30%

## Builder Title generator

Deterministic - hash of name + stack picks one entry from each table.

Prefixes: Beachside, Sunburnt, Feni-Fuelled, Barefoot, Monsoon, Low-Latency, Susegad,
Midnight, Salt-Crusted, Prompt-Drunk

Nouns: Backend Bandit, Frontend Fiend, Systems Shaman, Shipping Machine, Latency
Lifeguard, Deploy Daredevil, Schema Sorcerer, Pixel Pirate, Infra Islander, Whisperer
of Palolem

A Reroll control advances the seed for anyone who wants a different one.

## App UI

Mobile first, one screen, no routing.

    +---------------------+
    |  HH GOA 2026        |  compact header, sunset wordmark
    |  +-------+-------+  |
    |  |  PFP  |  ID   |  |  format tabs
    |  +-------+-------+  |
    |  +---------------+  |
    |  |    PREVIEW    |  |  live canvas, drag to move, pinch to zoom
    |  +---------------+  |
    |  o o o o            |  variant chips
    |  [ name          ]  |  Format B fields only
    |  [ @handle       ]  |
    |  [ stack         ]  |
    |  title: ...  reroll |
    +---------------------+
    | [ Download ][Share] |  sticky bottom bar
    +---------------------+

- Empty state loads public/sample.jpg immediately so a judge sees finished output in
  under a second without uploading anything
- Touch targets >= 44 px, inputs font-size 16px to stop iOS zoom on focus
- Micro interactions: chip press ripple, confetti burst and light haptic on download
- Copy is playful and short: "Drop a photo. Get framed."

## Asset checklist

- [ ] sun-halftone.svg
- [ ] palm-frond.svg (left + mirrored right)
- [ ] wave-bands.svg
- [ ] azulejo-tile.png (seamless, 256 px)
- [ ] grain.png (seamless noise, 512 px)
- [ ] sample.jpg (deliberately off-centre landscape, proves cropping works)
- [ ] Fonts: ClashDisplay-Bold, SpaceGrotesk-Medium, JetBrainsMono-Regular (woff2)