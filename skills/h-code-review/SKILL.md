---
name: h-code-review
description: Code review. Use when the user asks to review code/changes/branch, or after complex implementations (auto-upgraded by h-implement).
---

# Code Review

## Two Axes

- **Standards**: Conformance to repository rules (AGENTS.md Hard Rules / philosophy / craft) + **Fowler smell baseline** (below); repo rules take precedence (override smells); smells are judgment calls, not hard violations; skip what tooling already enforces
- **Intent**: Check against design intent (task content / user statements / planning consensus): missing or partial implementation, scope creep (things not asked for), incorrect implementation — cite the intent source item by item

## Fowler Smell Baseline

- **Mysterious Name** — name doesn't reveal purpose → rename; inability to name honestly signals unclear design
- **Long Function** — overly long function (multiple responsibilities strung together by time) → extract sub-functions by intent; long functions are hard to read, hard to test, hide duplication
- **Duplicated Code** — same logical shape in multiple places → extract shared
- **Feature Envy** — method touches another object's data excessively → move to where the data lives
- **Data Clumps** — same group of fields/params repeatedly appear together → bundle into a type
- **Primitive Obsession** — primitives instead of domain concepts → build dedicated types
- **Repeated Switches** — repeated switch/if cascades on the same type → polymorphism or shared mapping
- **Shotgun Surgery** — one logical change scattered across files → consolidate into one module
- **Divergent Change** — one file changed for many unrelated reasons → split
- **Speculative Generality** — abstraction for unneeded scenarios → delete; build when real need appears
- **Message Chains** — long chain a.b().c().d() → first object hides traversal
- **Middle Man** — intermediary that only forwards → call target directly
- **Refused Bequest** — subclass ignores most of inheritance → switch to composition

## Comment Review (Standards check item)

Core criterion: **if this comment is deleted, what information does the reader lose that isn't in the code?** If nothing, delete.

- **Receipt pollution**: comment restating what the code already says (`(no X)`, `(default N)`, literal translation of the name) → delete; if the signal is a bad name → rename first
- **History pollution**: corrected/abandoned artifacts, provenance, evolution comparisons (`originally pulled from XX`, `from XX research`) → delete; provenance not recorded
- Keep: behavioral contracts (commit only on success / 0 = disabled), data compatibility, external library pitfalls, transaction boundaries, test comments

Scan: go through every comment one by one (don't skip), judge in its containing declaration/context

## Report

Two axes **kept separate** (`## Standards` / `## Intent`, not merged or reordered); list findings per axis + a one-line summary (count + most severe item).

## Trigger

- Explicit user request ("review code/changes")
- Auto-upgrade after h-implement implementation (changes needing quality checks: complex / large blast radius / forming contracts)