---
name: refactor
description: "Use this when code works but needs restructuring (duplication, complexity, hard-to-read) WITHOUT changing behavior. Writes characterization tests FIRST to capture current behavior, then restructures, then proves the same tests still pass. NOT for adding behavior (that's a feature change). NOT for fixing bugs (that's systematic-debug)."
---

# refactor

Restructure code without changing observable behavior. The discipline: characterization tests first, then refactor, then prove the tests still pass. If the tests pass before AND after, behavior didn't change.

## When this skill fires

Auto-fires on:

- *"Refactor X"*
- *"Clean up X without changing behavior"*
- *"Restructure X"*
- *"This code is ugly, let's clean it up"*

Do NOT use for:

- Adding new behavior (that's feature work — go through brainstorm/spec/plan)
- Fixing bugs (that's `systematic-debug`)
- Adding tests to code that has none (run `audit-subsystem` first to assess coverage gaps)

## What this skill does

1. **Characterize current behavior** — write tests that capture what the code *currently* does (not what it *should* do). These are the safety net.
2. **Run the tests** — confirm they pass against current code.
3. **Refactor** — restructure for readability, simplicity, or maintainability. Behavior stays the same.
4. **Re-run the tests** — same tests must still pass. If they don't, behavior changed; revert and try a smaller refactor.

## Hard rule — behavior must not change

If the refactor breaks any characterization test, behavior changed. Either:
- Revert the refactor (preferred), OR
- Stop and surface to the user — was the original behavior wrong (this is a bug fix, not a refactor)?

Refactor without changing behavior. Period.

## Full instructions

Complete behavior contract — characterization test patterns, refactor moves, when to stop and call it a feature change — in [`../../commands/refactor.md`](../../commands/refactor.md).

## Related skills

- Often follows `audit-subsystem` (which reveals what's worth refactoring)
- After refactor: separate-agent verification to confirm nothing broke beyond the tests
