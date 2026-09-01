---
name: h-implement
description: Implementation workflow. Use when the user asks to implement a plan/task, or says start implementing.
---

# Implementation workflow

Drive autonomously to completion; stop to ask the user only for external-tracking registration, unclear requirements, or acceptance.

1. **Task tracking**: todo in-session by default; cross-session changes use external tracking (ask the user to register); execute per the planned task structure, marking status explicitly
2. **Implement**: follow the discussion above / planning consensus / explicit tasks, checked against philosophy and craft; sync docs in the same turn; requirement/plan problems loop back to `h-planning`; self-verify (run tests/smoke per the project's testing conventions); complex changes go through `h-code-review`
3. **Deliver**: self-review against the acceptance criteria, ask for acceptance