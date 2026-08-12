# Build Plan

Ordered so that after every milestone there is something shippable. If time runs out,
whatever is done is still a valid submission.

## M0 - Scaffold (~30 min)
- [ ] create-next-app: TypeScript, App Router, Tailwind
- [ ] Self host fonts in public/fonts, wire @font-face + document.fonts.ready
- [ ] lib/render/theme.ts: palette, variant ramps, type tokens
- [ ] Deploy an empty page to Vercel on day one, never debug hosting at 11 PM

## M1 - Upload and decode (~1.5 h)
- [ ] Uploader.tsx: drop zone, file input, clipboard paste
- [ ] loadImage.ts: createImageBitmap with imageOrientation 'from-image'
- [ ] heic.ts: detect HEIC by extension + magic bytes, lazy heic2any
- [ ] Downscale anything over 3000 px long edge
- [ ] Error states: bad type, over 25 MB, decode failure
- Checkpoint: any photo including iPhone HEIC appears on a canvas

## M2 - Format A PFP frame (~3 h)
- [ ] coverFit.ts + circular clip
- [ ] primitives.ts: arcText, roundRect, grain, halftoneSun, waveBand
- [ ] drawPfp.ts: full layer stack per DESIGN.md
- [ ] 4 variants wired to VariantPicker
- [ ] Circular preview mask so users trust the X crop
- Checkpoint: a genuinely on-brand PFP renders live

## M3 - Download (~45 min)
- [ ] renderExport(): fresh 1600 px canvas, dpr 2, same renderScene
- [ ] download.ts: toBlob -> object URL -> anchor download -> revoke
- [ ] Verify the file opens at full resolution outside the browser
- Checkpoint: the task is minimally satisfiable from here

## M4 - Share to X (~2.5 h)
- [ ] caption.ts: caption + #FrameInGoa
- [ ] shareToX.ts: navigator.canShare({files}) path first
- [ ] api/upload/route.ts -> Vercel Blob -> { slug, url }
- [ ] app/i/[slug]/page.tsx + generateMetadata with summary_large_image
- [ ] Optimistic upload as soon as the graphic settles
- [ ] Verify in X Card Validator - non negotiable
- Checkpoint: all required flows complete

## M5 - Format B Builder ID (~3.5 h)
- [ ] IdForm.tsx: name, handle, stack, debounced
- [ ] hash.ts + builderTitle.ts + reroll
- [ ] Builder ID number
- [ ] drawIdCard.ts: header, photo slot, name auto-fit, title plate, data rows,
      tear line, QR, footer
- [ ] Lazy qrcode -> ImageBitmap
- [ ] FormatTabs.tsx sharing one upload between both formats
- Checkpoint: both formats delivered

## M6 - Real photo controls (~1.5 h)
- [ ] Pointer events drag to reposition
- [ ] Wheel and pinch zoom, clamp scale to 1..3
- [ ] Clamp offsets so the slot can never show empty area
- [ ] Optional FaceDetector auto-centre with silent fallback
- [ ] Reset framing button

## M7 - Polish (~2 h)
- [ ] Sticky mobile action bar, safe area insets
- [ ] sample.jpg preloaded zero-click demo
- [ ] Confetti and haptic on download
- [ ] Skeleton free: no spinners except the HEIC inline hint
- [ ] Landing metadata, favicon, title, description
- [ ] Lighthouse mobile pass >= 90

## M8 - QA and submit (~1 h)
Run the full matrix, then submit the live link to the form.

## Test matrix

| Case | Expected |
|------|----------|
| iPhone HEIC portrait | converts, correct orientation, framed |
| Landscape 16:9 | centre cropped, no distortion |
| Panorama 3:1 | cropped sanely, still usable |
| Tiny 200x200 | upscales without bad artefacts, no crash |
| 25 MB 48 MP JPEG | downscales, renders under 2 s |
| Rotated EXIF JPEG | auto corrected |
| PNG with alpha | composites on card background, no black box |
| Non image file | friendly inline error |
| Very long name, 30+ chars | auto fits, never clips |
| Empty Format B fields | sensible placeholders, still exports |
| Emoji or non-latin name | renders, no tofu |
| iOS Safari share | native sheet with image attached |
| Desktop Chrome share | intent opens, OG preview shows the graphic |
| Airplane mode after load | preview and download still work |

## Priority if time is short

Must ship: M0-M4 (upload, Format A, download, share)
Should ship: M5, M6
Nice to have: M7 polish, face detection, landscape ID variant

## Pre-submission checklist

- [ ] Live URL loads on a cold mobile Safari session
- [ ] No login wall, no signup gate anywhere
- [ ] Download produces a real PNG file
- [ ] X Card Validator shows the generated graphic
- [ ] Caption contains #FrameInGoa
- [ ] Works one pass, start to finish, without a reload
- [ ] Form submitted before 11:59 PM, 13 Aug 2026