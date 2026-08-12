# FrameInGoa - HH Goa 2026 Frame / ID Card Generator

Upload a photo, get a branded Hack House Goa 2026 graphic, download it or post it
straight to X. No login. No signup. One pass, start to finish.

Built for the HH Goa 2026 shortlisting task.

## What it does

Format A - PFP Frame: a circular HH Goa ring that wraps your photo into a ready-to-use
X profile picture. 1600x1600 PNG, four themes.

Format B - Builder ID Card: an event-badge style card with your photo, name, stack and
a generated Builder Title, Builder ID number and QR code. 1600x2000 PNG.

## Highlights 

- Instant - rendered entirely in browser on canvas, no server round trip
- Real photos - HEIC, EXIF rotation, portrait / landscape / panorama, cover-fit
  cropping plus drag to reposition and pinch to zoom
- Private by default - your photo never leaves the device unless you share
- Working X share - native share sheet attaches the actual image on mobile, desktop
  falls back to an intent URL whose OG preview shows the real graphic
- Mobile first - sticky action bar, safe area aware

## Stack

Next.js 14 App Router, TypeScript, Tailwind, Canvas 2D, Vercel Blob, Vercel

## Getting started

    npm install
    npm run dev

Open http://localhost:3000

### Environment

    BLOB_READ_WRITE_TOKEN=   # Vercel Blob, only for the desktop share flow
    NEXT_PUBLIC_SITE_URL=    # e.g. https://frameingoa.vercel.app

## Docs

| Doc | Contents |
|-----|----------|
| docs/PRD.md | scope, user flow, functional and non functional requirements |
| docs/ARCHITECTURE.md | stack, file layout, render pipeline, share subsystem |
| docs/DESIGN.md | brand concept, palette, type, layout specs |
| docs/BUILD_PLAN.md | milestones, test matrix, submission checklist |

## Share caption

Locked in for Hack House Goa 2026. Make your own -> <link> #FrameInGoa