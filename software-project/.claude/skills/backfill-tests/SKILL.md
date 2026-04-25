---
name: backfill-tests
description: "Use this to add tests to existing code that has no test coverage (or thin coverage). Tests capture CURRENT behavior — not desired behavior, not aspirational. NOT for new code (that's TDD as part of execute-plan). NOT for fixing bugs (that's systematic-debug, which may produce a regression test as a side effect)."
---

# backfill-tests

Capture the current behavior of existing code in tests. The point is to create a safety net — once tests exist, you can refactor or change the code with confidence.

## When this skill fires

Auto-fires on:

- *"Backfill tests for X"*
- *"Add tests to X"*
- *"Cover X with tests"*
- *"X has no tests"*

Do NOT use for:

- New code being written from scratch (write tests as part of `execute-plan`, not after)
- Specifying *desired* behavior (that's `write-spec` or `brainstorm` — design first, then code, then test)

## What this skill does

- Reads the target code to understand actual current behavior
- Identifies test cases:
  - Happy path (typical input → typical output)
  - Edge cases (empty input, null, boundary values, large input)
  - Error paths (what happens when things go wrong)
  - Side effects (what state changes, what's logged, what's emitted)
- Writes tests that document *what is*, not *what should be*
- If a test reveals existing buggy behavior, captures it as the test (with a comment) — flagging the bug to the user separately. Do NOT change behavior in this skill.

## Hard rule — capture current, not desired

A characterization / backfill test asserts current behavior. If the code does X, the test asserts X — even if X is wrong. The test is the safety net for refactoring; "fixing" current behavior in the test makes the safety net useless.

If you find genuinely buggy behavior while backfilling, surface it as a separate concern. The fix goes through `systematic-debug` or feature work, not through this skill.

## Test format

Tests can use Gherkin-style structure (Given/When/Then) for readability — see the vocabulary primer in [`../../../hodos.md`](../../../hodos.md). The Hodos template does NOT enforce a BDD framework; Gherkin is just a useful structure for thinking about scenarios.

## Full instructions

Complete behavior contract — test patterns, edge-case enumeration, how to handle discovered bugs — in [`../../commands/backfill-tests.md`](../../commands/backfill-tests.md).

## Related skills

- Often invoked before `refactor` (characterization tests are the safety net)
- After `audit-subsystem` flags low test coverage as yellow/red
- If buggy behavior is found: surface it for `systematic-debug` rather than changing it here
