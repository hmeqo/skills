# agent skills

A personal collection of skills for coding agents. After installation, agents automatically load workflow rules in matching scenarios, helping you manage documentation, development, and debugging.

## What's included

| Directory                           | What it is                                                                                                                                                                                       |
| ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `skills/h-agent-docs/`              | **Documentation system management**: generates AGENTS.md (agents.md format) + docs/ as needed, automatically injects engineering philosophy; maintenance mode: drift repair; rewrite mode: rewrites docs wholesale to a good state |
| `skills/h-commit/`                  | **Commit workflow**: infers changes from diffs of historical commits, writes commit messages; level of detail/format follows project conventions                                                  |
| `skills/h-planning/`                | **Planning workflow**: brainstorming / requirement clarification / design confirmation (decision tree + frontier) → convergent decision. Explicitly triggered                                     |
| `skills/h-implement/`               | **Implementation workflow**: implements per context/task (follows the task from prior context; otherwise checks existing tasks and asks which one) → self-test → code-review when needed → your acceptance |
| `skills/h-code-review/`             | **Code review**: two axes (conventions + intent) + Fowler code smells, from an independent reviewer's perspective (not self-review)                                                              |
| `skills/h-debug/`                   | **Debugging workflow**: confirm expectations first → reproduce (feedback loop) → verify hypotheses with you → fix after your approval → acceptance                                               |
| `skills/h-research/`                | **Topic research**: background delegation + primary sources (official docs/source code/spec) + references persisted to disk (docs/research/), producing a detailed report                         |
| `skills/h-agent-docs/references/`   | **Convention library**: engineering philosophy + per-language conventions (Python / Rust / TypeScript) + format templates (AGENTS/ADR/glossary/research/docs)                                     |
| `global/AGENTS.md`                  | **Global rules**: minimal guardrail set (comment discipline/corrections are replacements/commit initiative, etc.)                                                                                |
| `AGENTS.md` + `docs/`               | This repo's own documentation system (CONTEXT semantics / architecture overview, used when agents maintain this repo)                                                                            |

## Installation

```bash
npx skills add hmeqo/skills
```

Global rules (optional):

```bash
curl -fsSL https://raw.githubusercontent.com/hmeqo/skills/main/global/AGENTS.md -o <your global AGENTS.md location>
```

## Usage

Just talk to the agent in plain language to trigger:

- **"build a documentation system / generate project docs"** → documentation system generation (h-agent-docs)
- **"maintain project docs / sync docs / docs are outdated"** → documentation maintenance (h-agent-docs maintenance mode)
- **"documents are badly written / terms are outdated or wrong / docs are full of noise or missing content / fully rewrite docs"** → documentation rewrite (h-agent-docs rewrite mode)
- **"plan X / brainstorm X / help me clarify requirements"** → planning workflow (h-planning)
- **"implement task X / start implementing"** → implementation workflow (h-implement)
- **"there's a bug here / help me look into it"** → debugging workflow (h-debug)
- **"research X / look up the docs for X"** → topic research (h-research)

Skills auto-trigger when a conversation matches their description; they can also be invoked explicitly with `/skillname`.

## Interplay with external skills

Our skills cover the core workflows; the following skills from the [mattpocock/skills](https://github.com/mattpocock/skills) ecosystem step in for specific scenarios (no reinventing the wheel), install as needed (see table below):

- **Regular development/refactoring** (h-planning → h-implement): requirement confirmation uses the built-in decision tree (options + recommendation); specific scenarios use `domain-modeling` / `prototype` / `wayfinder` / `improve-codebase-architecture`
- **Debugging** (h-debug): routine debugging follows the staged flow (expectations → reproduce → hypothesis → approval → fix → acceptance); stuck on a hard bug/performance regression → `diagnosing-bugs` deep instrumentation (bisection/fuzz), then back to h-debug's approval gate and acceptance
- **Requirement grilling**: routine scenarios use the built-in decision tree; intense grilling when the user says "grill me" → `grilling`

| External skill                   | Scenario                                  |
| -------------------------------- | ----------------------------------------- |
| `grilling`                       | Intense requirement grilling ("grill me") |
| `domain-modeling`                | Polishing terminology disputes            |
| `prototype`                      | Prototype verification when UI/logic is uncertain |
| `wayfinder`                      | Planning very large workloads (cross-session) |
| `improve-codebase-architecture`  | Refactoring large legacy codebases        |
| `diagnosing-bugs`                | Deep diagnosis of hard bugs/performance regressions |

Install: `npx skills add mattpocock/skills`

## Philosophy

Type-driven design · DDD dependency direction · design by contract · explicit > magic · extract named functions · RAII guards · fix root causes not symptoms · verify external facts, don't guess.