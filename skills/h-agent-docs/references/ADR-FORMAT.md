# ADR Format (Architecture Decision Record Format)

> Based on mattpocock/skills' domain-modeling ADR-FORMAT; built in and self-contained, with no dependency on external skills.

**Do not create `docs/adr/` by default**; enable it only when finalized in Q3 (at the user's explicit request); ask the user for explicit consent before writing an ADR (external actions discipline — never create on your own).

ADRs live in `docs/adr/` (conventional location), numbered sequentially: `0001-slug.md`, `0002-slug.md`... Create the directory lazily on first use.

## Template

```md
# {short decision title}

{1-3 sentences: the background, what was decided, and why.}
```

That's all: an ADR can be a single paragraph. Its value is recording "a decision was made and why", not in filling out a format.

## Optional sections (add only when they carry real value)

- **Status** frontmatter (`proposed | accepted | deprecated | superseded by ADR-NNNN`) — useful when the decision may be revisited
- **Considered Options**: when rejected alternatives are worth remembering
- **Consequences**: when non-obvious downstream effects need pointing out; when present, include **positive/negative/neutral** ones (not only the good ones)

## Numbering

Scan `docs/adr/` for the highest number and add 1.

## When to write an ADR (all three conditions hold)

1. **Hard to reverse**: changing your mind later is expensive
2. **Confusing without context**: future readers will look at the code and ask "why was it done this way"
3. **The result of a real trade-off**: real alternatives existed and one was chosen for concrete reasons

Easily reversible / not confusing / no real alternative → skip.

### What counts as an ADR

- **Architectural shape**: monorepo, event-sourced write model, etc.
- **Cross-context integration patterns**: domain events vs. synchronous HTTP
- **Technology choices with lock-in**: database / message bus / auth provider / deployment target — not every library, only the ones that take a quarter to replace
- **Boundary and scope decisions**: "data belongs to context X, everything else only references IDs" — an explicit non-decision is as valuable as a decision
- **Deliberate deviation from the obvious path**: hand-written SQL instead of an ORM because of X, so the next engineer doesn't "fix" an intentional design
- **Constraints invisible in the code**: compliance limits, response-time contracts
- **Rejected alternatives (when the rejection is non-obvious)**: the subtle reason GraphQL was considered and REST chosen; otherwise someone proposes GraphQL again six months later