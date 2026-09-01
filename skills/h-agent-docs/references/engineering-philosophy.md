# Engineering Philosophy — Cross-Language Engineering Philosophy

## Code Principles (Working Style)

- **Focus on code structure**: before acting, consider how to fit in with / refactor the existing architecture; maintain readability, maintainability, and robustness
- **Research first**: research the best solution before writing code; check whether base/third-party libraries already provide a better implementation
- **Immediately codify repeatedly emphasized discipline**: discipline the user emphasizes multiple times (important, must be followed, easily forgotten) is immediately written into the standards (philosophy /Hard Rules /skill); the standards carry it, ensuring sustained compliance
- **Verify technical information**: external facts and technical claims (user statements, the current state a proposal depends on, standards cited in docs) are not taken from memory or guesswork — verify online and check timeliness (release time/version/maintenance status/whether superseded by newer technology) and reliability (official docs > community consensus > personal articles; cross-validate key decisions), then adopt after confirmation; when inconsistent with user claims, point it out with evidence; outdated/unavailable information is not adopted and an alternative is given; when it cannot be confirmed, mark [INFERENCE]. **Distinguish decisions from facts**: the user's technology choices/taste are decisions (respect them and provide options); the user's factual claims (including assumptions like "is X Y") are verified before responding; if wrong, point it out with evidence — don't vaguely agree
- **Elegant code first**: prefer refactoring to solve problems elegantly; meaningless duplication /hardcoding /over-coupling are defects; layer responsibilities clearly, extract named functions to encapsulate procedural logic; refactor on the basis of complete encapsulation and type safety — don't simplify for its own sake; consider the long-term overall architecture

## Comment Discipline

- **No explanatory comments**: code self-description first — good naming / extracted functions express intent; first consider refactoring so comments become unnecessary; needing an explanation is often a sign the code failed to express itself; mechanism/design explanations belong in docs
- **Comments describe current facts, not process and sources**: corrected/discarded intermediate artifacts, sources, and evolution comparisons are treated as never having existed; final code and comments reflect only the current state — no "previously/derived from/from"

## Prompt and Rule Writing

- **Positive-first**: what can be expressed as "do X" is not written as "don't do X"; piling up don'ts makes agents conservative and do less (consecutive don'ts without dos → over-exploration, less work)
- **Negatives retained in only two scenarios**: ① preventing the agent's default mode (agents fall back to the most common practice); ② when it cannot be expressed positively (only exclusion works)
- Key negatives and positives are used side by side; without explicit don'ts, agents tend toward the most common pattern

## Type-Driven Design

| Concept | Practice |
| --- | --- |
| Newtype wrapping primitive types | Wrap when it carries domain meaning; improper use adds complexity |
| Tagged unions for polymorphism | Preferred; inheritance only when a shared default implementation is needed |
| Value objects over bare object/map/dict | Don't implement data types with dict; use typed value objects for data models (dataclass/NamedTuple/BaseModel/serde struct/enum) — type-safe with no special cases |
| Enums over magic values | Variants are enumerated by the type system (enum/sum type) rather than documented conventions; no magic strings/numbers |
| Architecture over assertions | Express constraints with types and architecture (ownership/RAII, nullability and error value types, validation at construction); assertions only as a fallback when architecture cannot express it and the type checker cannot determine it, and they must be encapsulated, not scattered across callers |

Constraints expressible in types are not left to runtime.

## Responsibility Layering and Encapsulation

Responsibility division has two dimensions: the **conceptual layer** (divided by intent/requirements, the finest granularity) and the **architectural layer** (large-scale layering). **When responsibility assignment is unclear, ask the user to clarify**: conceptual boundaries / responsibility division are user decisions (domain intent); agents can find the current state (code/glossary) but not the intent. Don't guess, don't assume defaults.

### Conceptual Responsibility Division (by intent and requirements)

- **Conceptual encapsulation over functional encapsulation**: module boundaries are drawn along **conceptual** abstractions (top-level, abstract/stable); functionality, as the implementation content of a concept, sits at a lower level (concrete/changeable). When drawing boundaries, first ask "what concept is this", then settle "which functions belong to it"; cutting boundaries directly by function (function as module) changes frequently as implementations evolve, while conceptual boundaries change slowly (echoing "split modules by why they change")
- Domain concepts divide responsibility by **intent/requirements**: each concept's boundary (what it manages, what it doesn't) is decided by the **intent it serves**; within the same domain, split by purpose (e.g. "artifact ≠ asset", "layer ≠ pass", each managing its own intent)
- Conceptual boundary criterion: **"What is this concept's responsibility? What intent does it serve?"**. Two concepts serving different intents = separate; variants of the same intent = merge
- Implementation carriers: glossary (docs/CONTEXT.md; convention: each term = one conceptual responsibility unit) + type/module boundaries

### Architecture Selection and Layering (by what it depends on)

**Architecture is a technology choice (user decision); list candidate architectures with trade-offs, recommend DDD** (layered /DDD /hexagonal /ports-and-adapters /event-driven /CQRS, chosen per project):

```
domain (business rules) → application (use-case orchestration) → infrastructure (external world) → ui (presentation)
```

| Layer | Responsibility (what it manages) | Judgment basis |
| --- | --- | --- |
| domain | Business rules / domain concepts / invariants / algorithms | Does this logic depend on the external world? No (pure logic/data transformation) = domain |
| application | Use-case orchestration (composing domain operations + infra interfaces into use cases) | Does it just chain existing capabilities? Yes = application |
| infrastructure | Hardware / persistence / network / DLL / files | Does it touch external resources? Yes = infra, implementing interfaces defined by domain |
| ui | Presentation and interaction | Is it for the user to see? Yes = ui, carrying minimal business logic |

Dependency direction is one-way downward: domain has zero IO, zero framework, zero UI dependencies; application calls infra indirectly through traits/interfaces; infra implements traits, depending back on domain.

Criterion in one sentence: **layer by "what it depends on", split modules by "why it changes"**; modules within the same layer are aggregated by reason of change (high cohesion); across layers, only narrow interfaces cross (low coupling).

### Responsibility Encapsulation (Boundaries and Information Hiding)

| Principle | Practice |
| --- | --- |
| Minimal public API | The exposed interface contains only the operations callers need; no internal state/transient methods |
| Information hiding | Callers see only the contract (signatures/interfaces), never internals (data structures/algorithms/caches). "How much internal information does a caller need to know? The less the better" |
| Single responsibility | One module/class, one reason to change: "Why does it change?" — the answer should have a single subject |
| Narrow interfaces across boundaries | Cross-module communication uses narrow interfaces (functions/messages with explicit signatures); don't pass internal objects |

### Encapsulation (Extraction vs Abstraction, Timing)

- **Extract named functions (semantics, readability)**: hard-to-read / multi-step (>2 steps) code is **extracted immediately**; extract whenever possible; **separate intent from implementation** (readers know the intent from the function name without reading the implementation). Complex chained calls (`lst.map(...).filter(...).reduce(...)` etc.) with concrete meaning are semantically encapsulated (e.g. "extract the sum of factorials of the squares of all string lengths in the list" — a named function expresses the intent). Extraction products are naturally reusable (when the same intent appears a second time, call the existing function directly instead of extracting again)

| Level | Example | Encapsulation goal |
| --- | --- | --- |
| Collection chains | Filter/map/reduce chains, comprehensions | Named functions hide composition details |
| Multi-step initialization | create+start+parse+cleanup | Constructor + RAII guard |
| Infrastructure details | Raw SQL / serialization / protocol bytes | Named functions; callers don't touch non-domain concepts |
| Language mechanisms | Reference counting / copy-on-write / locks | Wrapper exposing a natural interface |

The goal is not to eliminate chains but to give procedural logic a name.

- **Abstraction/generalization (reuse design; generics/parameterization/interfaces/abstraction layers)**: **write twice, abstract on the third** (Rule of Three). When repetition appears, copy twice first. Duplication is far cheaper than the wrong abstraction. Only when the third occurrence's commonality is clear is the abstraction design correct. Abstract only when a real need appears
- Naming is documentation: a good function name > comments > de-encapsulated inlining

## Design by Contract

The caller satisfies the preconditions; the implementer guarantees the postconditions and invariants; violation means fail fast — errors propagate rather than being silently swallowed. Fallback only when an external interface explicitly defines a default value.

## Error Representation Choices

Which mechanism expresses errors is a technology choice made up front; once decided, it is uniform across the project: Rust `Result` (errors are values; callers handle them exhaustively), Python/TS exceptions (propagate along the call stack), C++ exceptions or error codes (per project).

- The choice is uniform, never mixed: no project with half exceptions and half return-value error checks
- Errors carry context: the error type/message states "what went wrong, why"; don't swallow exceptions, don't drop information (echoing Design by Contract)
- Typed errors across boundaries: define error types or error codes across modules/ends; don't judge errors with bare strings/magic values

## Explicit > Magic

- typed events instead of string-based
- enums/constants instead of magic strings/numbers
- `any`/`dynamic` banned project-wide
- named accessors instead of inline match/isinstance; variant-classification decisions concentrated in one place

## Cross-Boundary Type Synchronization

Manually syncing across two places = 100% chance of forgetting. Types shared across ends (frontend/backend split, Rust↔TS bridge inside a shell, between multi-language services) must be generated from a single source of truth; both ends hand-writing and maintaining their own copies is prohibited — each side maintaining its own set is double attention and inevitably drifts.

Make the technology choice first, settle the synchronization mechanism, and keep it uniform project-wide: frontend/backend split → the backend provides an OpenAPI schema to generate client types; Rust↔TS inside a Tauri shell → tauri-specta generates TS types from command signatures; between multi-language services → message protocols such as Protobuf; the backend automatically generates TS types consumed directly by the frontend.

Once the mechanism is set: contract change → regenerate → mismatch is exposed at compile time. Generated code is marked "do not edit manually".

## RAII Guard Pattern

Resource lifetime is bound to scope: acquired on initialization, released automatically on leaving the scope; callers don't worry about it. Normal returns and exception/early-exit paths release automatically as well; no explicit cleanup needed — eliminating leaks and forgotten releases.

The same idea across languages: C++ destructors, Rust `Drop`, Python `with`/context managers.

## Git Commit Discipline

- **Commits are user-initiated**: git commits (including amend/merge commits) are executed only when the user explicitly requests them
- **Commit messages are written against the historical diff**: the commit message states "what changed compared to the committed state, and why", against the diff of the previous commit — not written per session/work process (what a session did ≠ the commit message)

## External Actions

- **Creating/updating tracking entries and external content is user-initiated or requires explicit consent**: issues/PRs/comments/backlog entries/ADRs/decision records; don't create or modify content in external systems (GitHub etc.) on your own

## Privacy and Information Discipline

Credentials/keys/tokens, personal privacy, trade secrets, intranet/infrastructure details, and content the user explicitly asked not to record are **not written into the documentation system** (AGENTS.md / docs) or **tracking systems**; privacy information found in existing records is removed.

**Information sources are not recorded**: comments/docs don't note sources.

## Communication Style

- Code reference format: `file_path:line_number`
- Use emoji only when the user explicitly requests it
- Concise, evidence-driven: one fact/decision/risk per sentence