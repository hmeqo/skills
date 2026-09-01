# AGENTS-FORMAT — AGENTS.md Generation Format

The template, injection rules, and structure query basis by which `h-agent-docs` generates the project entry AGENTS.md. The structure follows the agents.md open standard (pure Markdown, section-based, README for agents).

## Structure

```
# <Project>

## Project overview
<One sentence: what the project is, tech stack, key concepts>

## Commands
<Install/build/test/run commands; project-specific commands (scripts/Makefile/CI hooks)>

## Code style
<CRITICAL> minimal guardrail set </CRITICAL>
<Deep-read link to the complete standards: cross-language philosophy + the corresponding language craft>

## Testing
<Test commands / when to run / acceptance criteria> (when there are tests)

## On-demand sections
<Not limited to Commit / PR / Security / Deployment — write only when the project has conventions>
```

- Pure Markdown; no numbered skeleton, no mandatory fields
- Write only what agents need to work efficiently: commands/style/testing/conventions; don't rewrite content that duplicates the README
- monorepo: subprojects use nested AGENTS.md (nearest takes precedence); the nearest file as read wins
- docs/ on demand: CONTEXT.md only when there are many terms, architecture.md only for complex architecture; don't default to the four-piece set

## Hard Rules Injection

- Comment discipline (no explanatory comments; mechanism explanations go to docs)
- Corrections are replacements (artifacts describe only current facts, not rejected/reverted attempts)
- Same-turn update of docs (when the project has docs/)
- Commit initiative (commits are user-initiated; messages against the final diff)
- External actions discipline (tracking entries/issues/PRs are user-initiated)
- Privacy discipline (credentials/privacy/secrets don't enter docs and tracking)

## Convention Query

On first generation, use ask (options + recommendation) to ask whether to standardize project conventions; if the project already has an existing mechanism or the user has preferences, adopt them directly, otherwise confirm the defaults. Final decisions are written into the corresponding AGENTS.md section (commit initiative /Code style, format decisions/Commits).

- **Change log carrier**: none (default) / external tracking (user-specified, e.g. GitHub Issues) / in-doc changelog
- **Commit conventions**: semantic prefix + concise factual style (default) / Conventional Commits / Git official 50/72 / Gitmoji / follow project history
- **Decision records**: none (default) / external tracking (same mechanism as the change log) / docs/adr/ (triggered by three conditions: hard to reverse + confusing without context + genuine trade-offs) / decision section in a topic doc
- **Commit initiative**: restricted (default, user-initiated) / unrestricted
