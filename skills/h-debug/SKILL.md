---
name: h-debug
description: Debugging workflow. Use when the user reports a bug, unexpected behavior, crash, or asks to investigate/fix something.
---

# Debugging Workflow

Build a feedback loop before hypothesizing; get consent before fixing after diagnosis is confirmed; acceptance passed means done.

## Diagnosis

1. **Clarify expectation**: check design intent/concept (bug may originate at the design layer — say explicitly "this is a design problem, not a code problem"); clarify boundaries; acceptance criteria come out together with expectations
2. **Build feedback loop**: build a tight reproduction signal first (a one-shot command/test/script that goes red for the target bug); if it can't be built, stop and say so clearly, ask for environment access/instrumentation permission; reproduction must match the symptom; minimize the failing case (bisect)
3. **Hypothesis generation and ranking**: 3–5 falsifiable hypotheses ("if X is the cause, changing Y will make the bug disappear"); show the ranked list to the user for reordering/exclusion; critically verify root cause (can an alternative explanation account for all phenomena?)
4. **Diagnostic methods combined as needed**: logs, instrumentation, reading code, checking git history to locate the introducing change, official docs, tentative modification to verify a hypothesis (log/instrument autonomously; logic changes require consent)

**Exit**: expectation, root cause, reproduction signal aligned before the consent gate; any unconfirmed item loops back to the corresponding step.

## Consent Gate

Present the complete solution set at once with a clear recommendation; critically examine each option (side effects / whether it masks the true cause); recommendation includes decision rationale; consider long-term / whole-project perspective, allow architecture changes. **User absent**: diagnosis/investigation may continue (inform them), **applying a fix requires user consent**.

## Implementation

1. **Fix**: against philosophy and craft; same-turn doc sync; **regression** (run reproduction signal to confirm the bug is gone + run related tests per project test conventions); clean up debug code (keep items with long-term diagnostic value at debug level with purpose noted)
2. **Acceptance**: list acceptance points and ask the user; failure loops back to the diagnosis block (re-clarify expectations)

**Multi-round iteration is normal**: new problems introduced by a fix (cascade/regression) also return to the diagnosis block. **Debugging involving large changes / cross-session goes to `h-planning`**.