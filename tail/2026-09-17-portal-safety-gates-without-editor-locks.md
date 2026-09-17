---
date: 2026-09-17
title: Today AI Learned: Freeze/hop safety without a HubSpot editor lock
type: learned
tags: [hubspot, cms, migration, freeze]
proof:
  # none yet — local scripts in hbi-dev; no PR yet
---

# Freeze/hop safety without a HubSpot editor lock

## Problem

Portal hops replay a Dev freeze onto staging/prod. Without gates, a seed can wipe unpublished editor work, claim a URL owned by a different page, or capture a page mid-keystroke. The ask was simple: stop if someone is editing, if a draft is ahead of published, or if a seed would overwrite a foreign page/URL.

## Decision

HubSpot’s public CMS pages API does not expose a live “who has this page open” lock. Gate on the signals that *are* reliable, and treat freeze and seed differently:

| Signal | Freeze (source) | Seed / hop (target) |
| --- | --- | --- |
| Auto-save buffer ahead of saved draft | Stop | Stop |
| Draft newer than published (or layouts differ) | Flag; still capture draft | Stop |
| Seed name/slug owned by a different page | n/a | Stop |

Shared helper: `scripts/portal_safety.py`, wired into freeze, hop, `seed-pages`, and `seed-content`.

We considered blocking freeze whenever draft ≠ published, but rejected it because freeze is *supposed* to capture saved drafts. Blocking only mid-edit (buffer ahead of draft) keeps that design. We also ignored sub-second `updatedAt` skew when layouts match — publish can bump timestamps without real unpublished work.

## Check

- Against live Dev: hop/seed refuse pages with draft-ahead (e.g. For the Church, Give Today); freeze notes them and continues
- Synthetic collision: slug `connect` owned by “Old Connect” → hard stop; same name + same slug → allowed (idempotent)
- Unsaved-buffer path is the best mid-edit proxy available; no public editor-session lock to assert further

## Result

`portal_safety.py` plus gates in freeze/hop/seed scripts; process note in `docs/08-portal-and-migration.md`. No PR yet.

## Reuse

When the platform has no editor lock, don’t invent one. Gate on buffer vs draft, draft vs published, and name/URL ownership — and make capture vs overwrite asymmetric when the tool’s job is to snapshot drafts.

---

### Social draft

Hook: HubSpot won’t tell you who has the page editor open.

Story: We asked freeze/hop to refuse mid-edit and draft-ahead overwrites. The agent looked for a lock field. There isn’t one on the public CMS API.

Fix and proof: Gate on auto-save buffer vs draft, draft vs published, and foreign name/URL collisions. Freeze still captures saved drafts; seed stops. Verified against Dev pages that already had unpublished drafts.

Takeaway: No lock API? Use the signals that exist — and don’t make freeze and seed share the same stop rules.
