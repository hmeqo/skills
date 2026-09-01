---
name: h-research
description: Topic research. Use when the user wants to research a topic, gather documentation or API facts, or delegate reading to background agents.
---

## Workflow

- **Background delegation**: dispatch background sub-agents to research (main workflow continues; reading chores outsourced)
- **Primary sources**: official docs / source code / spec / first-party APIs, not secondhand retelling; trace every claim back to the source that owns it (philosophy "verify technical facts")
- **Output**: per RESEARCH-FORMAT (detailed report: conclusion summary + detailed findings sections + source list + limitations; detail level decided by topic complexity)

## Persist Criteria

- User explicitly asks to save → save
- Valuable (long-term facts / later work depends on it) → ask the user (ask: save to docs/research/?) → user decides
- One-off (answer suffices) → answer in conversation

## Persist

- `docs/research/<topic>.md` (directory is the index, no README / no special conventions)