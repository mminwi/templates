---
name: analyze
description: "Full health check of a single subsystem — code hygiene, spec drift, test coverage with test plan recommendations, and ops/security. Produces a Green/Yellow/Red punch list. Formerly audit-subsystem."
---

# analyze

Full health check on one subsystem at a time. Produces a Green/Yellow/Red punch list covering code hygiene, spec drift, test coverage, and ops/security — plus concrete test plan recommendations.

## When this skill fires

Auto-fires on:

- *"Analyze X"* / *"Audit X"*
- *"How healthy is X"* / *"Check the health of X"*
- *"What shape is X in?"* / *"How's the code in X?"*
- *"Check for spec drift"* / *"Is the spec still accurate?"*
- *"Does X have enough test coverage?"* / *"What tests exist for X?"*
- *"Look at X before we change it"*
- *"What are we working with in X?"*
- *"Assess X"* / *"Review X"*
- *"Give me the state of X"*
- *"What's the quality of X?"*
- *"Before we touch X, what's there?"*
- *"Is X in good shape?"*
- *"Check X"*

## What this skill does

For the named subsystem, runs four parallel agents evaluating:

- **Code hygiene** — dead code, duplication, complexity, naming, unused dependencies
- **Spec drift** — claim-by-claim verification of spec canvases against code, silent omissions, renamed/relocated features, stale metrics
- **Test coverage** — what's tested, what's not, test plan accuracy, whether tests should be added (characterization of current behavior, not desired behavior)
- **Ops/security** — destructive actions without permission checks, unvalidated inputs, missing logging, hardcoded secrets, silent failures

Each finding gets a severity (CRITICAL / MODERATE / MINOR) and `file:line` reference.

The report includes:
- Green/Yellow/Red health grade
- Spec drift findings with categories (spec-wrong, code-wrong, both-wrong, undocumented)
- Test plan recommendations — what tests to add, what existing tests are stale, what `tests/TEST_PLAN.md` entries need updating
- Recommended next actions — offer to chain into `write-spec` for spec fixes or update `tests/TEST_PLAN.md`

Report goes to `plans/{YYYY-MM-DD}-analyze-{subsystem}.md`.

## Hard rule — assess, don't fix

This skill produces a report. The user triages and decides what to fix. Do NOT start refactoring or fixing during the analysis.

## Full instructions

Complete behavior contract — agent briefs, report template, test plan recommendations — in [`../../commands/analyze.md`](../../commands/analyze.md).

## Related skills

- Often run before `refactor` (analysis reveals what's worth refactoring)
- Spec drift findings chain into `write-spec` (targeted edit mode) for fixes
- Test recommendations chain into `tests/TEST_PLAN.md` updates
