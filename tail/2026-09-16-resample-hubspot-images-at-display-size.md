---
date: 2026-09-16
title: Today AI Learned: CSS max-width is not a resized image
type: reviewed
tags: [hubspot, images, performance]
proof:
  # none yet
---

# CSS max-width is not a resized image

## Problem

HubSpot image fields expose Size / Maximum width, but a custom module that only applies `max-width` in CSS still ships the original file. Accreditation logos and 48px icons were downloading 2000px uploads.

## Decision

Kept editor size and link settings in the markup. Rejected CSS-only shrink as the fix. Resample File Manager rasters with `resize_image_url` at the display size (1x + 2x `length`). Leave SVGs as vectors. Do not apply that pattern to viewport-relative photos that already have a 400–2400w srcset.

We considered resampling every user-uploaded image the same way, but rejected it because hero/split/story photos need a width ladder, not a logo-sized 1x/2x pair.

## Check

- Financial Transparency: Excellence in Giving logo `currentSrc` is `length=200` (200×200), not the 2000×2000 original
- Leadership Development: icon-cards request `length=48` / `length=96`; 2x file is 96×96
- Hero `poster` uses `resize_image_url(..., 1600)`; no published page has a hero video yet, so that path is markup-only

## Result

Theme updates in `hbi-theme` (`hb-logo-row`, `hb-icon-cards`, `hb-hero` poster). No PR yet.

## Reuse

When an image has a known CSS box, resample to that box. When it scales with the viewport, keep a srcset ladder. CSS `max-width` is not a download-size fix.

---

### Social draft

Hook: A 48px icon was still downloading a 2000px PNG.

Story: The first fix honored HubSpot's max-width in CSS. That made the logo *look* small. The file did not change.

Fix and proof: `resize_image_url` at display size (1x + 2x). DevTools showed `length=200` / `length=96` instead of the originals.

Takeaway: CSS shrink is not a resized image. Resample known-size assets; keep srcset ladders for photos.
