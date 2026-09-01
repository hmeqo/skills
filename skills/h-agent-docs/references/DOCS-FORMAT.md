# DOCS-FORMAT — Project Documentation System Format Specification

The **template, responsibilities, and boundaries** of each document type in the docs/ system; `h-agent-docs` generation and maintenance follow this.

## Organization Rules

- **Form**: collection-type → directory (`topics/`, `adr/`); single-document-type → file (`CONTEXT.md`, `architecture.md`)
- **Generate on demand**: create only when there is content worth recording (don't create for pure CRUD code)
- **Template sections on demand**: template sections are coverage checklists (write when there's content, omit empty sections), not fixed forms
- **Docs carry what code cannot express**: intent/decisions/pitfalls/terms/navigation. Content readable from source (entity lists/field tables/interface lists/symbol-list-style lists/algorithm details) is obtainable by grep/glob and doesn't occupy docs
- **Progressive disclosure**: reference between docs with links (e.g. `see docs/adr/0001-xxx.md`); write each piece of content in one place only (single source of truth)
- **Length is determined by content**
- **Cross-references**: cite locations between docs with `file:symbol` (line numbers go stale)
- **Formal tone**: docs synthesize discussion outcomes — write conclusions and evidence, self-contained content (readers have no conversation context); don't write the conversation process, don't mark process metadata (dates/implementation paths)

## Responsibility Overview

| Document | Responsibility | Timing | Format authority |
| --- | --- | --- | --- |
| `AGENTS.md` | Entry: minimal guardrail set + commands + testing + on-demand sections | Generation | `AGENTS-FORMAT.md` |
| `CONTEXT.md` | Terminology authority (IS not DOES / mixed-use adjudication / relations) | Generation/maintenance | `CONTEXT-FORMAT.md` |
| `architecture.md` | Overview skeleton: architecture declaration + key data flows + module navigation + evolution direction | Generation (research review; **not produced for new projects / code-less repos while requirements are undecided; planned and produced once requirements are settled**) | This document |
| `topics/<topic>.md` | **Mechanism/abstraction deep dives** (operation/boundaries/trade-offs, unreadable from code; **continuously maintained**) | Distilled after implementation + updated on evolution | This document |
| `adr/` | Decisions and reasons | Explicitly requested by the user (settled by Q3), triggered by three conditions | `ADR-FORMAT.md` |
| `docs/research/<topic>.md` | Research snapshot (one-off, cites external sources, detailed report) | Produced by h-research (on demand) | `RESEARCH-FORMAT.md` |

## Abstraction Design Content Ownership

| Abstraction content | Carrier |
| --- | --- |
| Requirements / boundaries / acceptance | Planning consensus (confirmed in discussion, lands with implementation) + tracking system (per project mechanism, if selected) |
| Concepts / semantics | CONTEXT.md |
| Decisions / design rationale | adr/ (on demand) + code comments (implementation-level trade-offs) |
| Planning / evolution | architecture.md "Evolution Direction" |
| **Mechanisms / abstractions** | **topics/<topic>.md (continuously maintained)** |
| Pitfalls | AGENTS.md Hard Rules (worth guarding against) + code comments (details) |
| Overview | architecture.md |

## architecture.md Template

```
# architecture

## Architecture Declaration
<One sentence: architecture type + layering + dependency direction>

## Key Data Flows
<Cross-module flows/data flows whose overall picture can't be seen without reading all relevant code>

## Module Navigation
| Module | Key files | Responsibility |

## Evolution Direction (on demand)
<Known roadmap / planned design changes>
```

**Writing notes**: the architecture declaration is the current state extracted by research (the user's initial explicit decision); the review judges whether it conforms to the philosophy principles and points out problems found at acceptance; not written for new projects / code-less repos while requirements are undecided — planned and produced once requirements are settled. Data flows use mermaid or text diagrams; each section is brief (architecture declaration one sentence, module responsibility one sentence); structure is carried by tables/diagrams.

## topics/<topic>.md Template (continuously maintained)

```
# <Topic>

## Overview
<What this mechanism/abstraction is and what it solves>

## Design
<How it operates / technical architecture / key flows>

## Boundaries
<What it does / what it doesn't do>

## Trade-offs and Reasons
<Key design trade-offs + why>

## Pitfalls / Common Mistakes
<Phenomenon + cause + response>

## Evolution Direction (on demand)
<Known change directions>
```

**Writing notes**: well-illustrated (mermaid diagrams combined with text). Continuously maintained as the project evolves (mechanism changes/refactors/additions; `h-agent-docs` maintenance mode performs same-turn updates).

**Should not have** (all documents): entity lists/field tables/interface lists, symbol-list-style lists, line-by-line translations of code logic. (Cross-reference anchors are allowed; what's forbidden is writing documents as symbol lists.)