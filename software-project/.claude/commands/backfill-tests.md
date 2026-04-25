---
description: Add tests to legacy code that never had them. Gherkin-first, one scenario at a time, bugs found get logged not fixed
---

# /backfill-tests

## When to use

Invoke when:
- A module has no tests and you want coverage before changing it
- An audit revealed low test coverage on a critical path
- You're about to refactor (`/refactor`) code that lacks characterization tests
- A bug revealed a gap — the scenario that broke had no test

Do **not** use to add tests to code you're actively changing in a Hodos run — that pipeline produces its own tests as part of Phase 1 (Gherkin) and Phase 3 (execute-plan).

## The backfill discipline

Backfill is a specific kind of test writing. You are not defining desired behavior — that's already in production. You are **documenting** what the code actually does, so future changes can be verified.

Same principle as `/refactor`'s characterization phase, but applied to code you're not necessarily refactoring. The goal is safety net, not feature definition.

## Process

### Step 1 — Pick a target

One file, one class, or one function at a time. Do not try to test an entire subsystem in a single run. Scope is:

- **Smallest useful unit:** one function, one class, or one module
- **Highest value first:** code that gets called a lot, code that handles money/security/data, code about to be refactored
- **Skip:** trivial wrappers, generated code, code you're about to delete

### Step 2 — Read the code

Before writing any tests, read the code thoroughly. Understand:

- What inputs does it accept?
- What outputs does it produce?
- What side effects does it have (database writes, external calls, logs)?
- What error conditions does it handle (or not handle)?
- What's the happy path? What are the edge cases?

If the code is too tangled to understand in one read, that's a signal: backfill-tests may need to be preceded by a refactor. Report this to the user and stop.

### Step 3 — Write Gherkin scenarios first

For each observable behavior, write a Gherkin scenario capturing what the code currently does.

```gherkin
Feature: {function or class name}

  Scenario: {behavior 1 — happy path}
    Given {starting state}
    When {function called with inputs X}
    Then {returns Y}
    And {side effect Z happens}

  Scenario: {behavior 2 — common error}
    Given {error-triggering state}
    When {function called}
    Then {returns error / raises exception / returns default}

  Scenario: {behavior 3 — edge case}
    Given {unusual input}
    When {function called}
    Then {current actual behavior — not what you think it should be}
```

**Critical:** the scenarios describe **actual** behavior, not **desired** behavior. If the function returns `None` on empty input, the scenario says "Then returns None" — even if you think it should raise an error. Capturing reality is the job. Changing behavior is a separate Hodos run.

### Step 4 — Implement tests one at a time

For each scenario:

1. Write the test case in the project's test framework (pytest, etc.)
2. Run it. It should pass against current code (because you described actual behavior).
3. **If it fails, investigate.** One of three things happened:
   - Your test is wrong — fix the test
   - You misread the code — re-read, fix the scenario
   - The code is buggy — this is a real finding, log it (see below)
4. Commit the passing test.

### Step 5 — Log bugs found during backfill

Sometimes writing a test reveals the code doesn't do what you expected. This is common with old code. Do not fix these bugs as part of backfill. Instead:

- Add an entry to the backfill plan file under "Bugs or suspicious behavior found"
- Capture the behavior in the test anyway (with a comment noting it's suspicious)
- Propose a follow-up Hodos run to fix the bug properly

The rationale: a fix changes behavior. Backfill captures current behavior. Mixing the two defeats the purpose of the safety net.

## Output format

Plan file at `.claude/plans/{YYYY-MM-DD}-backfill-tests-{target}.md`.

```markdown
# Backfill Tests — {target}

**Date:** {YYYY-MM-DD}
**Target:** {file / class / function}
**Reason for backfill:** {audit finding / pre-refactor prep / bug surfaced gap / general coverage}
**Status:** Draft / In progress / Complete

---

## Code under test

- **File:** `{path}`
- **Functions/methods being tested:** {list}
- **Dependencies mocked vs real:** {note}

---

## Scenarios to capture

- [ ] Scenario 1: {short description}
- [ ] Scenario 2: ...
- [ ] Scenario 3: ...
- [ ] Scenario 4 (edge): ...
- [ ] Scenario 5 (error): ...

---

## Gherkin

```gherkin
Feature: ...
  Scenario: ...
```

---

## Tests implemented

For each scenario, link to the test file and line:

- [x] Scenario 1 → `tests/test_foo.py::test_happy_path` — passing
- [x] Scenario 2 → `tests/test_foo.py::test_missing_input` — passing
- [ ] Scenario 3 → pending

---

## Bugs or suspicious behavior found

(Do NOT fix during backfill. These are follow-up Hodos tasks.)

- **{description}:** {file:line}. Captured in test X as current behavior. **Proposed follow-up:** {bug fix via Hodos, or confirm as intended}.

---

## Coverage after backfill

- **Before:** {percentage or "none"}
- **After:** {percentage}
- **Uncovered paths remaining:** {list — what still has no test}

---

## Out of scope

- **Bug fixes** — any bugs found during backfill are noted, not fixed
- **Refactoring** — if the code is hard to test, that's a refactor job, not a backfill job
```

## Rules

1. **One target at a time.** Don't try to cover a whole module in one session.
2. **Capture reality, not desired behavior.** If the code returns None, test that it returns None — even if you think it should raise.
3. **Bugs found = log, don't fix.** Keep backfill and bug-fix as separate activities.
4. **Scenarios first, then code.** Write Gherkin before test code. The Gherkin is the contract; the test is the implementation.
5. **Tests must pass against existing code.** If a test fails, the test is wrong or the code has a real bug — either way, stop and investigate before adding more.
6. **Don't expand scope.** If a function calls another function that's also untested, that's a separate backfill run.

## What you do NOT do in this skill

- Do not fix bugs found during backfill
- Do not refactor code to make it easier to test (that's a separate refactor job, done first)
- Do not add tests that describe intended behavior if current behavior differs — capture reality
- Do not skip bug-found logging — if the user doesn't know about it, it won't get fixed later
