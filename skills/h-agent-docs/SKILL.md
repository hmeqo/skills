---
name: h-agent-docs
description: "Documentation system management. Use when the user asks to create or rebuild agent docs for a project, reports outdated/drifted docs, wants old docs rewritten to the latest standard, or invokes h-agent-docs by name."
---

# agent-docs — Documentation system management

For any project, generate an **AGENTS.md entry** (structure in `references/AGENTS-FORMAT.md`) + on-demand docs/ (CONTEXT terms, architecture overview), and inject the engineering philosophy.

**Philosophy injection**: follow `references/engineering-philosophy.md` (cross-language principles) and the language-specific `references/*-craft.md`; the injection checklist is in AGENTS-FORMAT.md, "Hard Rules injection".

**Acceptance principle (applies across all flows)**: any process output is complete only when **the user accepts it**; otherwise, iterate back to the corresponding step until acceptance passes.

## Invocation

| `agent-docs` | Generate the documentation system for the current project; for another project, follow the conversation context |
| `agent-docs maintain` | Fix documentation drift (review + fix in one): after code changes, sync the docs by mapping change types |
| `agent-docs rewrite` | Rewrite docs wholesale to a good state: when quality degrades or standards evolve (outdated/incorrect terms, over-guidance, missing content, noise rules, structural problems, awkward wording) |

## Generation flow

### Research (identify the shape, don't assume)

- Has code and is well-structured → extract through research (full documentation system)
- No code (only docs/misc) → build only the AGENTS.md skeleton (philosophy injection); content docs are created lazily as they evolve
- Has code but messy → build only the skeleton + note "structure not organized; content docs to be created after cleanup"; don't extract navigation/architecture from messy code

**Determine the toolchain and commands (required)**: read lock files / package managers / test and build configs (pyproject.toml, package.json, Makefile, CI, etc.) to determine the toolchain and project-specific commands; confirm with the user, then write them into the AGENTS.md Commands section.

**Version-control check**: when there's no git, remind the user to decide on the spot (initialize, or skip the git discipline); with git, skip this.

Research output: module→file navigation, key decisions and rationale, dependency directions, pitfalls and open issues, domain terms. For complex/non-code projects, a scout subagent can run parallel read-only research.

**Research confirmation**: list all the output at once; the user confirms or corrects item by item; mark speculation `[INFERENCE]`; exposed ambiguity/trade-offs → the conventions query (below) or other option queries (options + recommendation, expanded when multiple options each have merits).

### Conventions query

On first generation for a project, use ask (options + recommendation, h-planning decision-tree style) to ask whether to standardize project conventions: the change log carrier, commit style, decision records. Adopt directly when the project already has mechanisms or the user has explicit preferences; otherwise confirm the defaults. Options and write locations are in `references/AGENTS-FORMAT.md`.

**Architecture statement (no query needed; lazy)**: for an existing project, the architecture is a fact of the current state — extract through research + review judgment (whether dependency directions are healthy), write into architecture.md; point out architecture problems (dependency inversion / confused layering) at acceptance. For new projects / code-free repos with undefined requirements, don't produce an architecture statement.

### Writing

**AGENTS.md (entry)**: generate per `references/AGENTS-FORMAT.md` (the structure template, injection checklist, and specialized options are all there). Pure Markdown, no numbered skeleton.

**docs/ (on demand, don't restate the code)**: templates/responsibilities/boundaries for each doc type are in `references/DOCS-FORMAT.md`. The CONTEXT.md format (when there are domain terms) is in `references/CONTEXT-FORMAT.md`; the architecture.md template (when the architecture is complex) is in DOCS-FORMAT.md. Documents carry only what code cannot express (intent/pitfalls/terms/decisions/navigation).

### Verification

- The files/symbols indexed by module navigation exist per grep (spot check)
- The Commands section's commands actually match the configuration
- Injected Hard Rules minimal (only what's needed, all executable, one sentence each)
- AGENTS.md readable within 2 minutes (the entry stays thin)

### Acceptance

The user checks the output (meets expectations, matches the code); complete only after acceptance passes. When generating, mark research-extracted pitfall/constraint items; at acceptance, prompt the user to verify them item by item (distinguish "project conventions" from "research extraction").

## Maintenance mode (review + fix in one)

Trigger: documentation drift/outdatedness (docs not synced after code changes; content doesn't match the implementation).

**Change type → doc mapping**:

| Change type                                                                    | Update target                                                                                                   |
| ------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------- |
| Module added/renamed/removed                                                   | `AGENTS.md` (when the overview/commands are affected) + `architecture.md` module index (create when applicable) |
| Domain concept or term added/changed                                           | `CONTEXT.md` (create when applicable)                                                                           |
| Project-level constraint raised by the user (worth hardening into a Hard Rule) | AGENTS.md Code style section (tell the user it has been recorded when hardening it)                             |
| New pitfall/debugging lesson                                                   | the "pitfalls/known behavior" section of the corresponding docs                                                 |
| Mechanism/structure change (refactor / new mechanism / abstraction change)     | the corresponding docs (architecture/mechanism detail, continuously maintained)                                 |

**Maintenance flow**: identify the change (read the diff/change description) → update the target docs per the mapping (anchors as "file:symbol") → register/update tracking (per the project mechanism, none by default; registration follows the external-actions discipline) → verify (key symbols exist per grep) → wrap up after acceptance. Docs don't match the actual code: clearly lagging the change → fix first, then continue; can't tell which side is the intent → ask the user to decide.

## Rewrite mode (rewrite docs wholesale to a good state)

Trigger: documentation quality degrades or the standard evolves — outdated/incorrect terms (not matching implementation), over-guidance, missing important content, noise rules, structural problems, awkward wording.

Audit against the implementation and the format templates, then rewrite the deviating items — content, terminology, structure, noise, completeness, wording. Acceptance passes only when the user accepts; tracking wrap-up follows the external-actions discipline.

## Documentation system quality requirements

1. AGENTS.md stays concise (minimal guardrail set, no padding), readable within 2 minutes
2. Doc maintenance is written into the Hard Rules: any code change updates the corresponding docs in the same turn; conflicts caused by changes are resolved in favor of the implementation, and the docs are corrected
3. Anchors are always "file:symbol" (line numbers go stale)
4. Change log criterion: **record only what forms a behavioral contract** (observable behavior / interface / persisted format / domain semantics); purely internal implementation is not recorded
5. Spec docs must include a "known behavior" section; open issues found during research are written in truthfully
6. Optional ADR: not created by default (decided in Q3); adopted only when all three conditions hold — "hard to reverse + confusing without context + a real trade-off" (format in `references/ADR-FORMAT.md`)

## Output layout (on demand)

```
<project>/
├── AGENTS.md             # entry
└── docs/                 # on demand (create only when there's something worth recording)
    ├── CONTEXT.md        # term authority (when there are domain terms)
    ├── architecture.md   # overview (when architecture is complex)
    ├── topics/           # mechanism detail (create only for complex mechanisms, continuously maintained)
```
