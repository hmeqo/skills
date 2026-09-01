# skills — agent skills collection

Personal agent skills collection by hmeqo (pure markdown, no code). Global agent rules (code principles / communication style) live in `global/AGENTS.md` (linked by the user per coding agent; single source of truth). This file holds only repo-specific rules and the skill trigger table; general principles are not duplicated here.

## Commands

- No build/test/run commands (markdown-only repo)
- Commit format (repo convention): semantic prefix + single-line header + body explaining why (motivation/trade-offs), concise; no em dash, no marketing words; message written against the final diff, not the session (see `skill://h-commit`)

## Code style

<CRITICAL>
- **Comment discipline**: no explanatory comments — naming/extract functions to express intent; consider refactoring before commenting; mechanism/design explanations go to docs, not code comments
- **Corrections are replacements**: treat corrections as overriding old requirements; final artifacts (code/comments/PR/commit messages) describe only current facts, never rejected/reverted attempts — "why there is no X" notes are pollution (receipts), delete
- **Repo conventions**:
  - Three-way consistency: new/modified skills must sync SKILL.md frontmatter ↔ README structure table ↔ trigger table (this file)
  - Naming: skills use `h-` prefix; references file names lowercase hyphens
  - `skill://` paths must match actual repo directories
  - References layering: cross-language principles → engineering-philosophy.md; language implementations → craft (python/rust/typescript); format templates → ADR/CONTEXT/DOCS-FORMAT.md
  - Same-turn update: any skill/doc change updates README structure table and trigger table in the same change; conflict resolved in favor of implementation and docs fixed
- **Verify external facts online**: tool/framework/spec status, versions, superseded or not; historical pitfall: opencode commit convention guessed from memory, verified as mandatory semantic prefix
- **Changes need user confirmation first**: present plan (what / how) before implementing; discussion ≠ change instruction (separate discussion from directives)
- **Commit initiative**: commits are user-initiated
- **External actions**: tracking entries/issues/PRs created by user initiative or explicit consent
- **Privacy**: credentials/personal privacy/business secrets/internal details/content user asked not to record never enter docs and tracking; sources not recorded in comments/docs

</CRITICAL>

Deep-read links (read on demand): `skill://h-agent-docs/references/engineering-philosophy.md` (cross-language philosophy) + language crafts (`python|rust|typescript`-craft.md).

## Skill Navigation

<CRITICAL>
Before starting any of the following tasks, you **must** read the corresponding skill:
</CRITICAL>

| Scenario | Skill |
| --- | --- |
| Planning / brainstorming / clarifying requirements (decision tree + frontier convergence) | `skill://h-planning` |
| Implementing per context/task | `skill://h-implement` |
| Standalone code review (two axes + code smells) | `skill://h-code-review` |
| Debugging (expectations → feedback loop → hypothesis → fix → acceptance) | `skill://h-debug` |
| Topic research (background delegation + primary sources) | `skill://h-research` |
| Generate/rebuild a project's agent documentation system | `skill://h-agent-docs` |
| Documentation drift/outdated (sync docs after code changes) | `skill://h-agent-docs` (maintenance mode) |
| Rewrite docs wholesale (outdated/incorrect terms, over-guidance, noise, missing content) | `skill://h-agent-docs` (rewrite mode) |
| git commit (commit workflow, infer changes from diffs of historical commits) | `skill://h-commit` |
| Cross-language engineering philosophy | `skill://h-agent-docs/references/engineering-philosophy.md` |
| Python project conventions | `skill://h-agent-docs/references/python-craft.md` |
| Rust project conventions | `skill://h-agent-docs/references/rust-craft.md` |
| TypeScript project conventions (no any/type-first) | `skill://h-agent-docs/references/typescript-craft.md` |
| Nuxt/Vue project conventions | `skill://h-agent-docs/references/typescript-craft.md` (Nuxt section) |
| alova useRequest/mutation/query | `skill://h-agent-docs/references/typescript-craft.md` (alova section) |
| Glossary (CONTEXT.md) authoring format | `skill://h-agent-docs/references/CONTEXT-FORMAT.md` |

## On-demand sections

- **Decision records**: none by default (Q3 decision); only when user asks (`docs/adr/` or decision section in topic docs)
- **Change log**: none (Q1 decision); no changelog, no decision tracking unless user asks