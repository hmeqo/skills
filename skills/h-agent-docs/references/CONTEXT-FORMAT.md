# CONTEXT.md Format — Domain Glossary Specification

> The format agent-docs follows when generating `docs/CONTEXT.md`. CONTEXT.md is the project's **authority on domain language**: it defines only what each word *is*, with no implementation details.

## Structure

```markdown
# <context name>

<one or two sentences: what this context is and why it exists>

## Language

**<term>**:
<one-sentence definition (what it IS, not what it DOES)>
_Avoid_: <synonyms, confusable terms>

## Relationships

- One **A** produces several **B**
- **B** belongs to exactly one **A**

## Example dialogue

> **Dev:** "..."
> **Domain expert:** "..." (shows the term boundaries)

## Flagged ambiguities

- "X" was once used to mean both Y and Z; resolved: they are distinct concepts
```

## Rules

- **Be opinionated**: when one term has multiple meanings, pick the best word and list the rest under `_Avoid_`
- **Flag conflicts explicitly**: conflated terms go into Flagged ambiguities with a ruling
- **Definitions matter**: one sentence that says what something *is*, not what it *does*
- **Relationships use bolded term names** to express cardinality (one / several / exactly one)
- **Only project-specific concepts**: generic programming concepts (timeout / error types / utility functions) are not included; ask yourself "is this concept unique to this context?"
- **Group by natural clusters** (subheadings); a single domain stays flat
- **Example dialogue**: a dialogue between a developer and a domain expert, showing how terms interact naturally and clarifying the boundaries of related concepts

## Example grouping (multi-domain projects; group names follow the project's actual domains, shown here as illustration only)

```
### Core domain     # core of the domain model
### Integration     # external systems / hardware
### Interface       # UI / interaction
### Persistence     # storage formats
```

## Multi-context projects

- Single context: `docs/CONTEXT.md` (conventional location)
- Multiple contexts: `docs/CONTEXT-MAP.md` lists each context's location and relationships; each context keeps its own CONTEXT.md

## Maintenance discipline

- Record term decisions as soon as they are resolved (no batching)
- Keep it in sync when new domain concepts are added (same-turn update when an agent changes code)
- On conflict with the implementation, **present the discrepancy to the user and rule on which one wins**: if the glossary truly reflects the intent (it drives naming), change the code; if the code reflects the intent, fix the glossary