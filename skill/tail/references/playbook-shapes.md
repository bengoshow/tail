# Playbook shapes for Tail writeups

Condensed from *The AI Developer Playbook* — use these when drafting a `/tail` entry.

## Proof trail

```text
PROBLEM → DECISION → CHECK → RESULT
```

Optional fifth beat when something lasting came out of it: **Reuse**.

### Problem

What needed to change, who it affected, and the goal or constraint.

### Decision

What you kept, changed, or rejected from the agent's code or plan — and why.

Pattern: "We considered [other option], but rejected it because [reason]."

### Check

The test, review, measurement, or reproduction steps that verify the decision.

### Result

Link the PR, tests, demo, or writeup so someone can check it. Never invent links.

### Reuse

Rule, checklist, skill, or agent instruction that prevents the same mistake next time.

## Review-catch note (type: reviewed)

```text
Problem: …
Risk: …
Decision: …
Evidence: …
Reuse: …
```

## Social post draft (optional footer)

1. **Hook** — one concrete line that stops a developer scrolling
2. **Story** — what the AI did and what you spotted (short lines)
3. **Fix and proof** — what you changed and the test that proves it
4. **Takeaway** — one thing others can copy, then a link to the write-up

## Entry types

| Type | Use when |
| --- | --- |
| `learned` | You learned something durable while steering AI |
| `reviewed` | You caught a real problem in review or redirected the agent |
| `fixed` | Agent output was wrong and you corrected + verified it |

## Decision log row

```markdown
| YYYY-MM-DD | Short decision summary | [tail/YYYY-MM-DD-slug.md](tail/YYYY-MM-DD-slug.md) |
```

## Filename

`tail/YYYY-MM-DD-slug.md` — kebab-case slug from the title; if taken, append `-2`, `-3`, etc.
