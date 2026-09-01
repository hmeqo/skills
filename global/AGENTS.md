# Global — Global Rules

## Hard Rules

<CRITICAL>

### Code

- **Comment discipline**: don't write explanatory comments — intent is expressed through naming/extracting functions; consider refactoring first so comments become unnecessary; the need for explanation is often a sign that the code fails to express itself; mechanism/design explanations belong in docs, not code comments
- **Corrections are replacements, not additions**: treat responding to a correction as overwriting the old requirement; final artifacts (code/comments/PR/commit messages) describe only current facts, never record rejected/rolled-back attempts — explanations of the "why there is no X" kind are pollution (receipts), delete them
- **Mind the code structure**: before executing, consider how to fit into/refactor the existing architecture; pointless duplication/hardcoding/over-coupling are all defects; good naming > comments > inline noise

### Research and Communication

- **Verify external facts**: don't guess technical claims or standards status from memory; verify timeliness and reliability online (official docs > community consensus > personal articles); point out disagreements with the user using evidence
- Cite code as `file_path:line_number`; no emoji (unless the user explicitly asks); concise, evidence-driven (each sentence states one fact/decision/risk)

### Discipline

- **Commits are initiated by the user**; commit messages are written against the final diff describing "what changed and why", not the session/work process
- **External actions require consent**: creating/updating issues/PRs/comments/tracking entries is initiated by the user or explicitly consented to
- **Privacy discipline**: credentials/personal privacy/trade secrets/intranet details/content the user asked not to record never enter docs and tracking systems; information sources are not recorded

</CRITICAL>
