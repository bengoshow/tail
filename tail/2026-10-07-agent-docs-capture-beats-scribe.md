---
date: 2026-10-07
title: Today AI Learned: Let the agent with the code and the browser write the admin docs
type: learned
tags: [documentation, training, wordpress, cursor, browser]
proof:
  # none yet. Commit lives in a private client repo (docs/admin-guide/, 22 guides, 83 screenshots)
---

# Let the agent with the code and the browser write the admin docs

## Problem

A nonprofit client's admin training is tomorrow: one hour for three admins on a custom WordPress block theme (custom blocks, the Site Editor, custom content types). An hour can't cover everything, so I also needed handoff documentation with screenshots for every area, including the ones we'd skip.

For a previous client I used Scribe for this. Scribe is a browser extension, so it couldn't run inside Cursor. I had to drive it through Claude in Chrome, reset the page, and start a new Scribe capture by hand for each workflow. It was hands-on, took longer, and produced 15 documents.

## Decision

This time I gave one agent all the context up front:

1. Opened the theme project in Cursor, so the agent could read the blocks, field definitions, and templates.
2. Opened Cursor's browser and logged into the staging site's admin myself.
3. Prompted for a one-hour agenda ("they know WordPress, focus on our custom theme, blocks, and the FSE experience"), then: "give me a plan to capture walkthrough documentation and screen captures for all of these areas, including things we won't have time to cover fully in the training hour."

Choices I made along the way:

- **Staging, not production**, for every screenshot. The agent was told not to save anything.
- **An editable Word file as the handoff**, which the client can open in Google Docs, built from **one Markdown file per guide**. If a feature changes, I edit that one section and regenerate the document.
- **Dropped the "Covered in training?" notes** from the handoff. They helped me plan, but they mean nothing to the client.

We considered recording workflows with Scribe again, but rejected it because a recorder only sees clicks. It can't read the code to explain what a field does, and every capture needs a person to start it.

## Check

- The agent wrote each guide from the theme code (field labels, help text, breakpoints, which blocks feed which pages), then checked the wording against the live admin screens.
- It caught and fixed its own wrong claims during the process (a contrast table, how the hero image behaves on mobile) by checking the code and computing the color ratios again.
- It blurred sensitive data before capturing, including the Google Maps API key on the Theme Options screen, plus emails and usernames.
- The build script checks that every link inside the document has a target and that the file stays under Google Drive's 50 MB import limit.

## Result

About 90 minutes for a 118-page Word document: 22 guides with 83 screenshots, a contents page, a troubleshooting FAQ, a "do not touch" list, and an accessibility checklist. Plus the training agenda. Compared with Scribe: less hands-on, faster, and broader coverage (22 guides versus 15 documents).

Proof: none yet (private client repo).

## Reuse

For client docs on a site you built, give one agent the repo **and** a logged-in browser on staging. Ask for one Markdown file per topic with a fixed layout, and generate the shareable document from those files. Tell it to blur keys and personal data, and never to save changes on the site.

---

### Social draft

Hook: 118 pages of client admin docs with screenshots, in about 90 minutes.

Story: For the last client I used Scribe. It's a browser extension, so I had to reset the page and start a capture for every workflow: 15 docs, lots of babysitting. This time the agent had the theme code open and a logged-in staging browser.

Fix and proof: It wrote 22 guides from the code, took and checked its own screenshots, blurred the API key, and built an editable Word doc from one Markdown file per section.

Takeaway: Docs come out better when the tool can read the code, not just record the clicks.
