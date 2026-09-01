---
name: h-commit
description: Commit workflow. Use when the user asks to commit changes or a commit message needs writing.
---

# h-commit — git commits

Write the commit message from the commit history — `git log` for style/granularity, `git diff HEAD` for what changed — NOT from a session summary.

Single-line header by default; add a body only when the change carries genuine motivation or trade-offs the diff can't convey (explaining what changed and why, not how — the code shows that).

No session process, marketing words, or empty talk.

Use project conventions when defined; otherwise follow the historical format; with no history, ask the user (options: header + why body / header only).

Check `git status` for scope — only relevant changes, no credentials/privacy/debug leftovers.