# Context — skills repo

This repository's domain language authority.

## Language

**skill**:
A workflow/convention unit that a coding agent can load (a directory containing SKILL.md).
_Avoid_: "skills" (as a generic capability), "plugins"

**skill collection**:
The whole repository (multiple skills + global rules + documentation system).
_Avoid_: "skill" (singular is ambiguous)

**references**:
The catalog directory of convention documents under h-agent-docs (philosophy / craft / format templates).
_Avoid_: "convention library"

**craft**:
The language conventions in references (python / rust / typescript-craft).

**trigger table**:
The "task type → skill" mapping (in global/AGENTS.md: guidance for agents deciding which skill to use; kept in sync with the README structure table).

**same-turn update**:
The discipline that changing a skill/doc requires updating the corresponding docs (README structure table, trigger table) in the same turn; when the docs and the implementation conflict due to a change, the implementation wins.

**topic docs**:
docs/topics/<topic>.md: in-depth explanations of mechanisms/abstractions (behavior/boundaries/trade-offs that can't be read from code); **continuously maintained** (updated as things evolve, topical notes style).

## Relationships

- A **skill collection** contains several **skills**
- A **skill** belongs to exactly one **skill collection**
- **references** contains several **craft** documents
- A **change** corresponds to one **task**

## Flagged ambiguities

- "skill" was once used to mean both an individual skill and the whole collection; resolved: use "skill collection" for the collection
- "ticket" (intermediate artifact of an execution slice) has been rejected: impl implements directly from context; task decomposition is internal organization at the impl layer; tracking entries are uniformly "task"