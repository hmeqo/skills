---
name: h-planning
description: Planning workflow. Use when the user asks to plan/design/scope a feature or change, or wants requirements clarified before implementation.
---

# Planning workflow

Anchor the current state first, then plan the change; organize requirements/design into a decision tree, asking the entire frontier each round (see below).

## Anchoring the current state (when one exists)

Read existing code/concepts/behavior to establish the change baseline (how the current state works, who depends on it). For each decision, ask against the current state: "what does this change in the current state, who is affected, is it a replacement or an addition?".

## Requirements and intent (do first)

Clarify the why first (what problem it solves / what goal it serves); **open confirmation** (the user expresses freely, no multiple-choice questions); intent takes precedence over all details. Boundaries (what to do / what not to do). Restate your understanding (inferences included) so the user can confirm alignment. **Proactive critical digging**: brainstorm to diverge and surface directions, then converge; proactively find points the user hasn't noticed (requirement blind spots / technical risks / unconsidered boundaries); hold opinions.

## Critical discipline

Throughout planning, proactively find and critically raise all issues (requirement blind spots / technical risks / plan flaws / unconsidered boundaries / impact on existing code); raise them at any time, not waiting for the user to ask. Even after the user approves a plan, self-review is still required; approval doesn't mean the plan has no flaws — don't relax criticism because of agreement.

## Decision tree (query)

Organize requirements/design into a **decision tree**; each round, ask the entire **frontier** (all previously settled decisions it depends on): multiple options each with merits → options + recommendation; a single optimal solution → confirm or raise objections. After the answers, recompute the frontier and ask the next round. Look up facts verifiable in the environment yourself; necessarily derived items are not decisions and don't enter the tree. Design decisions are checked against the general engineering philosophy (`skill://h-agent-docs/references/engineering-philosophy.md`, the normative source).

**Direction**: advance along the frontier (the dependency order): once intent is confirmed, start from the **decisions most directly dependent on intent** (scope/boundaries first: fix the scope before going deeper into details), expanding layer by layer along dependencies. Changes to any node propagate along the links: the affected decisions/concepts are re-reviewed along with it; touching the foundation (the intent/concept layer) means re-reviewing every decision that depends on it, restructuring the concept layer wholesale if necessary.

**Cross-layer impact**: design problems return to the concept layer for correction; implementation constraints that affect earlier decisions are confirmed early (technical feasibility / hardware / SDK limits); problems during implementation loop back to overturn the design.

**Two-way discussion**: both sides freely raise views and question each other (critically examine the requirements; your own understanding/plans are also open for discussion with the user; hold opinions, can hold a reasonable position, and be willing to adjust). Convergence = the frontier is cleared **and the plan survives both sides' questioning** (the user accepts the understanding; the plan is confirmed to objectively hold, until both sides accept it). **Before reaching consensus, ask yourself: is anything missing? Is it real consensus? Only when the self-check passes is it convergence; otherwise keep looping**.

## Design confirmation (when there's UI)

ASCII sketch (layout/controls/interaction shape) + functional flow diagram (data flow/state transitions, mermaid or text).

## Plan confirmation

Present the complete plan set at once + a clear recommendation: raise the key trade-offs one by one as questions; attach a decision-rationale summary to the recommendation (why it was chosen, the reasons the other options were ruled out); critically examine each plan (side effects / whether it masks the real cause); proactively hunt for plan blind spots (unconsidered scenarios / excluded alternatives / untested assumptions); consider the whole project and the long term, architecture changes allowed; when a plan depends on external facts, verify online (official docs as the authority, checking timeliness).

## Planning evolution and re-review

At key milestones (after layer transitions / major decisions), re-review the already-confirmed parts (consistency/omissions/conflicts); handle issues per the loop-back discipline. When planning completes, do a final review; only confirm consensus once the plan has no issues; loop back on omissions/conflicts.

## Loop-back discipline

Any change touching concepts/ownership/intent → return to requirements and intent to re-clarify; touching design/plans → return to the corresponding item; touching existing concepts (a new requirement changes existing semantics/ownership) → re-organize the existing concepts at the concept layer (mapping/evolution of old and new concepts); touching existing code (a new requirement changes existing behavior/structure) → propose a migration/adaptation plan at the implementation layer (assess the impact surface). Each change is implemented after confirmation; non-decision items (necessarily derived / implementation choices without trade-offs) are handled on your own.

- **Design intent**: after consensus, update the docs to the current state under the new design (term/architecture/mechanism descriptions follow the new design); docs describe only the current state, not the migration process (see git for the old design). New mechanism topics are hardened after implementation
- **Plan pending implementation**: when planning consensus is reached but implementation doesn't happen in this session, follow the project documentation system's carrier decision (AGENTS §4/§5): designated tracking → register after consulting the user; decision "none" → consult the user, then export per their choice (e.g. a task checklist as the carrier)
- Pausing is allowed after consensus: implementation can be triggered at any time (`h-implement` runs per context/task without re-planning). **Stop after planning consensus is reached; don't auto-enter implementation; implementation is explicitly triggered by the user.**