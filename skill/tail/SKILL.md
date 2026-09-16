---
name: tail
description: >-
  Summarize the current conversation into a Today AI Learned (/tail) writeup
  and post it to the configured Tail journal repo. Use when the user runs /tail,
  asks to write a TIL, or wants to capture what they learned, reviewed, or fixed
  while working with AI.
disable-model-invocation: true
---

# Tail — Today AI Learned

Turn the current conversation into one public, checkable writeup and commit it to the Tail journal repo.

## When to use

- User invokes `/tail`
- User asks to post a Today AI Learned / TIL / decision log entry from this chat
- User wants to capture something they **learned**, **reviewed** (or influenced), or **fixed**

Do **not** run this skill unless the user explicitly asked for it.

## Config

Read `config.json` next to this `SKILL.md` (same folder as the installed skill: `~/.cursor/skills/tail/config.json`).

Expected fields:

```json
{
  "repoPath": "/absolute/path/to/tail",
  "remote": "origin",
  "branch": "main"
}
```

If `repoPath` is missing, empty, or not a git repo:

1. Ask once for the absolute path to their local Tail clone (or a clone URL).
2. If they give a URL and no local path, clone it to a sensible location they confirm (for example `~/src/tail`).
3. Write or update `config.json` with `repoPath`, `remote` (default `origin`), and `branch` (default `main`).
4. Continue.

Never treat the *current working project* as the Tail journal unless its absolute path equals `repoPath`.

## Workflow

1. **Load the template and shapes**
   - Prefer `repoPath/templates/til.md` if it exists.
   - Otherwise use the copy under this skill's references, or the shapes in `references/playbook-shapes.md`.

2. **Pick one primary lesson** from the conversation:
   - `learned` — a durable lesson about steering AI
   - `reviewed` — a review catch or direction change you drove
   - `fixed` — a defect in agent output you corrected and verified

   Prefer judgement over a feature dump. One entry, one decision.

3. **Draft the writeup**
   - Filename: `til/YYYY-MM-DD-slug.md` (UTC or local date; kebab-case slug from the title).
   - Frontmatter: `date`, `title`, `type`, optional `tags`, optional `proof` links.
   - Body sections in order: **Problem**, **Decision**, **Check**, **Result**, optional **Reuse**.
   - Optional **Social draft** footer (hook → story → fix/proof → takeaway).
   - Scrub secrets, tokens, private URLs, customer data, and proprietary code. If the work cannot be shared, rewrite a minimal public version of the *same decision*.
   - Never invent proof links. Omit them or set proof notes to "none yet".

4. **Preview for the user**
   - Show the proposed filename, type, title, and a short summary (or the full draft if short).
   - Ask to confirm before writing—unless they already said "post it", "ship it", "commit it", or similar.

5. **On confirm, publish**
   - Ensure `repoPath` is clean enough to work in: `git status`, fetch if needed, checkout `branch`.
   - Write the file to `repoPath/til/YYYY-MM-DD-slug.md`.
   - Append one row to `repoPath/DECISION-LOG.md`:
     `| YYYY-MM-DD | Short decision summary | [til/YYYY-MM-DD-slug.md](til/YYYY-MM-DD-slug.md) |`
   - Stage only those journal files (not unrelated dirty files in the Tail repo).
   - Commit with a message like: `til: <title>`
   - Push to `remote` / `branch`.
   - If push fails (auth, no remote, diverged history), report the error and leave the local commit in place with recovery steps—do not force-push unless the user explicitly asks.

6. **Reply**
   - File path, commit hash/summary, and the optional social blurb.
   - Remind them they can link this entry from a PR, profile, or social post.

## Quality bar (playbook audit)

Before posting, the entry should pass:

1. **Checkable** — link, diff, test, or recording someone can inspect (or honest "none yet")
2. **Specific** — names the problem and the decision
3. **Your part clear** — obvious what you did vs what the AI did
4. **Proven** — how you know it worked
5. **Reusable** — someone else can learn from it (Reuse section when there is a lasting rule)

## Guardrails

- Explicit invocation only.
- One writeup per `/tail` run unless the user asks for more.
- Do not modify files outside `repoPath` for publishing.
- Do not overwrite existing `til/*.md` files; pick a new slug if the name collides.
- Do not co-author commits or add trailer credits unless the user asks.
- Keep the tone concrete and engineering-first — no hype, no "AI is magic".
