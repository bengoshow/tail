---
date: 2026-10-21
title: "Today AI Learned: Sanity's defineLive ignores document-type cache tags"
type: fixed
tags: [sanity, nextjs, isr, caching, webhooks]
proof:
  docs: https://github.com/sanity-io/next-sanity#revalidating-content-changes-from-automations
  # code: private repo
---

# Sanity's defineLive ignores document-type cache tags

## Problem

A newly published post had a working page of its own but never appeared in the site's post listing. Other published edits had the same problem; nobody had noticed yet.

The site fetches content with next-sanity's `defineLive`. In production, it caches published queries indefinitely and tags them `sanity` plus some opaque sync tags. The webhook route called `revalidateTag(body._type)`, so a new post cleared a tag like `"post"`. No query had that tag. The webhook returned 200 on every publish and cleared nothing. `<SanityLive />`, which would otherwise refresh the cache, only rendered in draft mode, so public visitors never got it.

The route was based on next-sanity's older tag-based revalidation example. That example assumes you tag your own `client.fetch` calls. `defineLive` doesn't work that way.

## Decision

The first fix changed the webhook to `revalidateTag("sanity", { expire: 0 })`. It worked, but checking the next-sanity README showed it was only half of the recommended setup. Sanity says to always render `<SanityLive />` and to use the webhook as a fallback for changes made while nobody has the site open. The final setup does both:

- `<SanityLive />` renders for every visitor. Visual editing stays behind draft mode.
- The webhook expires the `sanity` tag on publish, unpublish, and delete.
- `{ expire: 0 }` instead of `"max"`, so the first visitor after a publish doesn't get one more stale page.

Per-type tags on each fetch were ruled out. Queries join across types (an author can appear on posts, team pages, and the footer), so a per-type list would always miss something. Clearing everything was cheap here because the pages already render on every request.

## Check

- The Sanity webhook log showed a 200 one second after the publish, so the webhook fired and the route was the problem.
- The live listing HTML had older post slugs but not the new one.
- In the installed next-sanity source, `sanityFetch` defaults to `tags = ["sanity"]`. The browser listener only reacts to events while it's connected, and the read token only goes to the browser in draft mode.
- In local dev, as a normal visitor, the page connected to Sanity's live events endpoint without a token and the new post appeared first.
- Type-check and lint passed.

## Result

Four files changed: the site layout, the revalidate route, a tags helper, and the README. The live listing showed the new post after the fix.

## Reuse

With `defineLive`, render `<SanityLive />` for everyone and have the webhook call `revalidateTag("sanity", { expire: 0 })`. Skip `revalidateTag(_type)` unless every fetch is tagged by hand.

When an agent reports a fix, check it against the vendor's recommended setup. The first fix here worked, but it was only the fallback.

---

### Social draft

Hook: A Sanity webhook returned 200 on every publish and cleared nothing.

Story: The route cleared a cache tag named after the document type. next-sanity's `defineLive` never uses that tag, and the component that would have refreshed the cache only ran for editors.

Fix and proof: The webhook now clears the `sanity` tag, and `<SanityLive />` runs for every visitor. Checked against the webhook log, the package source, and a local session as a normal visitor.

Takeaway: With `defineLive`, revalidate `sanity`, not your document types.
