# Tail — Today AI Learned

A public journal of judgement from AI-assisted work, plus a Cursor skill (`/tail`) that turns a conversation into a checkable writeup.

Working code is cheap. What still matters is what you kept, changed, or rejected — and how you checked it. Tail follows the proof trail from *The AI Developer Playbook*: **Problem → Decision → Check → Result** (plus **Reuse** when you captured a lasting rule).

## What's in this repo

| Path | Purpose |
| --- | --- |
| [`tail/`](tail/) | Dated Today AI Learned writeups |
| [`DECISION-LOG.md`](DECISION-LOG.md) | Short table of decisions with links to proof |
| [`templates/tail.md`](templates/tail.md) | Canonical writeup template |
| [`skill/tail/`](skill/tail/) | Versioned Cursor skill source |

## Install the `/tail` skill globally

From a clone of this repo:

```bash
mkdir -p ~/.cursor/skills
cp -R skill/tail ~/.cursor/skills/tail
cp ~/.cursor/skills/tail/config.example.json ~/.cursor/skills/tail/config.json
```

Edit `~/.cursor/skills/tail/config.json` and set `repoPath` to the absolute path of this clone:

```json
{
  "repoPath": "/absolute/path/to/tail",
  "remote": "origin",
  "branch": "main"
}
```

Reload skills (restart Cursor or re-open Agent) and run **`/tail`** in any project.

### Cloud Agents

Personal skills in `~/.cursor/skills/` stay on your machine until you sync them. Open **Settings → Agents → Sync Skills for Cloud Agents** so Cloud Agents can use `/tail` too.

### Updating the skill

After pulling changes to this repo:

```bash
cp -R skill/tail/* ~/.cursor/skills/tail/
# keep your existing config.json — do not overwrite it with the example
```

## How `/tail` works

1. You finish a session where you learned something, influenced direction in review, or fixed agent output.
2. Run `/tail` (explicit only — it does not auto-fire).
3. The agent drafts one writeup from the conversation using the template.
4. After you confirm (or if you already said to post/ship), it writes `tail/YYYY-MM-DD-slug.md`, appends a decision-log row, commits, and pushes.

Entry types:

- **`learned`** — something you learned while steering AI
- **`reviewed`** — a review catch or direction you influenced
- **`fixed`** — something wrong in agent output that you corrected and verified

## Create this as a public repo

This project starts without a public remote name of its own. After you publish it (for example with Cursor's **Create repo** and a name like `tail` or `today-ai-learned`):

1. Clone it locally.
2. Point `repoPath` in `config.json` at that clone.
3. Run `/tail` from any workspace to post new entries here.

## Proof audit (keep entries honest)

Before publishing, check:

1. Can someone check it? (link, diff, test, or recording)
2. Is it specific? (names the problem and decision)
3. Is your part clear vs the AI's?
4. Did you prove it worked?
5. Can someone else learn from it or reuse it?

## License

Use and share freely for your own proof trail. No warranty.
