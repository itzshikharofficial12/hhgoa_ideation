# Architecture

## Stack

| Layer | Choice | Why |
|-------|--------|-----|
| Framework | Next.js 14 App Router + TypeScript | OG image routes, easy Vercel deploy |
| Styling | Tailwind CSS | fast iteration, tiny output |
| Rendering | Canvas 2D on the client | instant, offline, pixel exact |
| HEIC decode | heic2any, dynamic import | only reliable browser option |
| QR codes | qrcode, dynamic import | Format B only, code split |
| Storage | Vercel Blob | one line uploads, public URLs for OG |
| Hosting | Vercel | edge OG rendering, zero config |
| Fonts | self hosted woff2, subset | no FOUT, no canvas taint |

Key decision: the graphic is rendered entirely on the client. The server is only
touched to host a PNG so X can scrape an OG preview.

## Directory layout

    app/
      layout.tsx                 root shell, fonts, metadata
      page.tsx                   the single screen tool
      i/[slug]/page.tsx          share landing page with OG tags
      api/upload/route.ts        POST blob -> { slug, url }
    components/
      Uploader.tsx  PhotoStage.tsx  FormatTabs.tsx
      VariantPicker.tsx  IdForm.tsx  ActionBar.tsx  CanvasPreview.tsx
    lib/
      image/  loadImage.ts  heic.ts  coverFit.ts  faceCenter.ts
      render/ renderer.ts  drawPfp.ts  drawIdCard.ts  primitives.ts  theme.ts
      content/ builderTitle.ts  hash.ts
      share/  download.ts  shareToX.ts  caption.ts
    public/
      fonts/  art/  sample.jpg
    docs/

## Data model

    type Format = 'pfp' | 'id';

    type Transform = { scale: number; offsetX: number; offsetY: number };

    type IdFields = {
      name: string; handle: string; stack: string;
      title: string; idNumber: string;
    };

    type Scene = {
      format: Format;
      variant: 'sunset' | 'ocean' | 'coconut' | 'midnight';
      bitmap: ImageBitmap;
      transform: Transform;
      fields: IdFields;
      dpr: number;
    };

All state lives in one useReducer in page.tsx. No global store.

## Render pipeline

    File
     |- isHeic? yes -> lazy import heic2any -> Blob(jpeg)
     |          no  -> Blob as-is
     |- createImageBitmap({ imageOrientation: 'from-image' })
        |- downscale if long edge > 3000
           |- rAF draw loop -> preview canvas
              |- on export: fresh canvas at 1600px, dpr 2 -> toBlob

Preview and export call the same renderScene(ctx, scene) with a different dpr, so
what you see is exactly what you download. All coordinates are authored in a
1000-unit design space and scaled by size/1000.

## Cover-fit math

    slotAspect  = slotW / slotH
    imageAspect = imgW / imgH

    if imageAspect > slotAspect:      // wider -> crop left/right
        drawH = slotH * scale
        drawW = drawH * imageAspect
    else:                             // taller -> crop top/bottom
        drawW = slotW * scale
        drawH = drawW / imageAspect

    overflowX = drawW - slotW
    overflowY = drawH - slotH
    x = slotX - overflowX/2 + offsetX * overflowX/2
    y = slotY - overflowY/2 + offsetY * overflowY/2

Offsets clamped to [-1, 1] so the photo can never expose empty slot area. Handles
portrait, landscape, square and panoramic inputs with one function.

## Share subsystem

    Download: renderExport() -> Blob -> createObjectURL -> anchor download -> revoke

    Share (mobile)
      file = new File([blob], 'hhgoa-2026.png', { type: 'image/png' })
      if (navigator.canShare?.({ files: [file] }))
          navigator.share({ files: [file], text: caption })

    Share (desktop)
      POST /api/upload -> { slug }
      open https://x.com/intent/tweet?text=...&url=<origin>/i/<slug>

app/i/[slug]/page.tsx exports generateMetadata with:
  openGraph.images = [{ url: pngUrl, width: 1600, height: 1600 }]
  twitter.card = 'summary_large_image'

The page shows the graphic large with a "Make yours" CTA back to the tool, so every
share is also an acquisition loop. Upload fires optimistically the moment the graphic
settles, so the tweet window opens instantly.

## Performance plan
- heic2any, qrcode and face detection are all await import()-ed on demand
- Textures decoded once and cached as ImageBitmaps in a module-level Map
- Preview canvas capped at 720 CSS px, export renders separately at 1600 px
- Draw calls coalesced via requestAnimationFrame, dragging never re-decodes
- Blob URLs revoked after download
- No spinners anywhere except the HEIC inline hint

## Privacy and security
- Photos stay in memory on-device; nothing uploads unless desktop Share to X is used
- Uploaded blobs are public-read but keyed by an unguessable random slug
- No cookies, no third party image CDN
- Only same-origin assets drawn to canvas, so toBlob always succeeds