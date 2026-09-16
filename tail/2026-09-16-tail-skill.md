---
date: 2026-09-16
title: A /tail skill that posts Today AI Learned writeups
type: learned
tags:
  - cursor
  - skills
  - proof
proof:
  # Fill in after you publish this repo publicly
---

# A /tail skill that posts Today AI Learned writeups

## Problem

Working with AI produces real judgement every day — what to keep, reject, and verify — but that signal usually stays trapped in a chat. Without a place you control, there is no checkable trail for profiles, interviews, or teammates.

## Decision

Build a Cursor skill invoked as `/tail` (“Today AI Learned”) that summarizes the current conversation into one markdown writeup and commits it to this public journal.

We considered dumping notes into the project being worked on, but rejected that because proof should live somewhere shareable and separate from proprietary codebases. We also considered auto-running the skill on every session; rejected that so `/tail` only fires when you choose to publish a lesson.

Writeups follow the playbook shape: Problem → Decision → Check → Result, plus optional Reuse, with types `learned`, `reviewed`, or `fixed`.

## Check

- Skill installs to `~/.cursor/skills/tail/` and appears as `/tail`
- A writeup lands under `tail/` with the required sections
- `DECISION-LOG.md` gains a matching row with a real link
- Secrets and private URLs are scrubbed before commit

## Result

This repository is the Tail home: versioned skill source, templates, decision log, and TAIL entries. The example entry is this file.

## Reuse

Install the skill globally, point `config.json` at your local clone, and run `/tail` after any session where you learned something, caught a review issue, or fixed agent output.

---

### Social draft (optional)

Hook: AI chats disappear. Judgement shouldn't.

Story: I kept proving things in Cursor conversations and losing the trail by the next project.

Fix and proof: `/tail` turns one conversation into a Problem → Decision → Check → Result writeup in a public repo I control.

Takeaway: Keep the full example somewhere you own, then link out from PRs and social posts.
