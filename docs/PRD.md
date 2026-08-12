# PRD - HH Goa 2026 Frame / ID Card Generator

Codename: FrameInGoa
Deadline: 11:59 PM, 13 Aug 2026
Deliverable: one live public URL, no login, no signup gate.

## Goal
Upload photo -> branded HH Goa 2026 graphic -> download or share to X.
Budget: under 5 seconds, zero page reloads, zero spinners.

## Scope
Both formats in one app with a tab toggle. One upload feeds both.

### Format A - PFP Frame
- 1600x1600 PNG, transparent background
- 4 variants: Sunset, Ocean, Coconut, Midnight
- Arc text "HACK HOUSE GOA . 2026", sun/palm/wave motifs, bottom BUILDER chip

### Format B - Builder ID Card
- 1600x2000 PNG portrait, plus 1600x900 landscape variant
- Fields: name, X handle, stack/role
- Generated: Builder Title, Builder ID number, QR code to X profile

## Required flow
1. Upload photo - jpg, jpeg, png, webp, heic, heif
2. Fill fields - Format B only
3. Generate - near instant, client side canvas
4. Download - real png via canvas.toBlob()
5. Share to X - pre-filled caption with #FrameInGoa, image visible in preview

## Functional requirements

### FR-1 Upload
- Drag and drop, tap to pick, paste from clipboard
- HEIC/HEIF converted in browser via lazily loaded heic2any (WASM)
- Max 25 MB, downscale to 3000 px long edge before draw
- Inline errors: unsupported type, too large, decode failed

### FR-2 Real photo handling
- Cover-fit: scale to fill, centre crop. Never letterbox, never distort.
- EXIF orientation read and corrected
- Drag to reposition, pinch or scroll to zoom
- Optional face auto-centre via FaceDetector, silent fallback

### FR-3 Generation
- Client canvas only
- Fonts awaited via document.fonts.ready before first draw
- Preview throttled with requestAnimationFrame, export rendered once on demand

### FR-4 Download
- canvas.toBlob('image/png') -> object URL -> anchor download
- Filename hhgoa-2026-pfp-<slug>.png or hhgoa-2026-id-<slug>.png

### FR-5 Share to X
1. Mobile: navigator.canShare({files}) then navigator.share() with the real PNG File
   attached plus caption. This genuinely attaches the image.
2. Desktop fallback: upload PNG to blob storage, get slug, open
   https://x.com/intent/tweet?text=<caption>&url=<origin>/i/<slug>
   The /i/<slug> page sets og:image to the PNG and twitter:card=summary_large_image.

Caption: "Locked in for Hack House Goa 2026. Make your own -> <link> #FrameInGoa"

### FR-6 Builder Title generator
Deterministic. Hash of name + stack (FNV-1a) indexes prefix x noun tables, so a title
is stable and feels assigned. Reroll button nudges the seed.

### FR-7 Builder ID number
HHG-2026- plus 4 digits from the same hash. Stable across reloads.

## Non functional requirements

| Area | Target |
|------|--------|
| First render after upload | under 1.5 s for a 12 MP JPEG |
| HEIC conversion | under 3 s with inline hint |
| Lighthouse mobile perf | 90 or above |
| First load JS | under 200 KB gzipped |
| Layout | mobile first, sticky bottom action bar |
| Accessibility | keyboard operable, labelled inputs, 4.5:1 contrast |
| Privacy | photos never leave the device unless Share to X is used |

## Out of scope
Accounts and auth, print ready output, video output, public gallery, admin panel.

## Success criteria
- [ ] Works end to end on a cold mobile Safari session
- [ ] iPhone HEIC photo generates successfully
- [ ] Landscape off-centre photo frames well with no distortion
- [ ] Downloaded file opens as a real PNG at full resolution
- [ ] X Card Validator shows the generated graphic
- [ ] Caption pre-fills with #FrameInGoa
- [ ] Output is unmistakably Hack House Goa 2026

## Risks

| Risk | Mitigation |
|------|-----------|
| X intent cannot attach images | navigator.share on mobile, OG image link on desktop |
| HEIC decode is heavy | lazy load WASM only for HEIC files |
| Fonts render late | await document.fonts.ready, self host woff2 |
| Blob latency slows share | upload optimistically once the graphic settles |
| Canvas tainting blocks export | same-origin assets only |