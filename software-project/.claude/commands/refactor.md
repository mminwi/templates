---
description: Refactoring with characterization-first discipline — capture current behavior with tests, refactor, same tests still pass
---

# /refactor

## When to use

Invoke when the user wants to:
- Clean up code that works but is ugly
- Extract duplicated logic into a shared function
- Rename variables, functions, or modules for clarity
- Restructure a module for better organization
- Improve readability without changing behavior

Do **not** use when behavior needs to change. Mixing refactor + behavior change is the single biggest source of regressions. If behavior must change, do that in a separate Hodos run, after refactoring (or before).

## The characterization-first discipline

Refactoring has one rule: **the observable behavior does not change.** The same inputs produce the same outputs. The same side effects happen. Users see nothing different.

The problem: if you refactor without tests, you cannot prove the behavior didn't change. "It looks right" is not proof. Bugs introduced during refactor are silent — they surface weeks later as "the system used to do X and now it doesn't."

The solution: **characterization tests.** Before touching the code, write tests that describe what the code currently does. Run them. Confirm they pass. Then refactor. Run them again. Confirm they still pass. Any difference = bug.

Critically, characterization tests capture **reality, not intent.** If the code has a quirk — a rounding behavior, an edge case, an off-by-one — the test captures the quirk. This is deliberate. If the quirk is a bug, that's a separate change (a Hodos run) after the refactor is done.

## Process

### Phase A — Characterize

1. **Identify the scope.** Which function, class, or module is being refactored? What are the observable outputs (return values, side effects, database writes, log lines)?
2. **Write characterization tests.** Every observable output gets a test. Cover happy paths, error paths, and edge cases. Do not skip edge cases — those are often where refactor bugs hide.
3. **Run the tests.** Confirm all pass against the current code.
4. **Commit the characterization tests.** This commit has message like "Add characterization tests for X (pre-refactor)." Now there's a git record of what the behavior was before any changes.

### Phase B — Refactor

5. **Make the structural change.** Extract, rename, reorganize, split. Do not change behavior.
6. **Run the characterization tests.** If any fail, you changed behavior — stop and fix. If all pass, the refactor is safe.
7. **Refactor incrementally.** Small steps. Run tests after each step. A big-bang refactor with all tests failing at the end is unfixable.

### Phase C — Verify

8. **Run the full test suite**, not just the characterization tests. Refactors often touch things called by other tests.
9. **Invoke `/review-work`** to compare before/after and confirm nothing user-visible changed.
10. **Commit the refactor.** Commit message should describe the structural change — "Extract calculate_discount into a shared util" — and explicitly state "No behavior change" in the body.

## Handling quirks and bugs found during characterization

If, while writing characterization tests, you find behavior that looks like a bug:

1. **Do not fix it during the refactor.** That breaks the discipline.
2. **Note it separately** — add an entry to the refactor plan file under a "Behavior quirks found during characterization" section.
3. **Decide with the user:** is this a bug to fix, or intended behavior? If a bug, file it as a separate Hodos task AFTER the refactor is complete.

The characterization test captures the quirk as-is. Once the refactor is done, a separate Hodos run can change the behavior (and update the test) as a deliberate, user-approved change.

## Output format

Write a plan file at `.claude/plans/{YYYY-MM-DD}-refactor-{name}.md`.

```markdown
# Refactor — {name}

**Date:** {YYYY-MM-DD}
**Scope:** {function/class/module being refactored}
**Goal:** {why — extract shared logic, improve naming, restructure, etc.}
**Status:** Draft / Characterizing / Refactoring / Complete

---

## Observable behaviors (what the characterization tests capture)

- {Behavior 1} — returns X when input is Y
- {Behavior 2} — writes to table Z when condition W
- {Behavior 3} — logs warning "foo" when called with empty input
- ...

---

## Phase A — Characterization

- [ ] Write test for {behavior 1}
- [ ] Write test for {behavior 2}
- [ ] Write test for {behavior 3}
- [ ] Run tests — confirm all pass
- [ ] Commit: "Add characterization tests for {scope} (pre-refactor)"

## Phase B — Refactor

- [ ] {structural change 1, e.g., "extract helper function X"}
- [ ] {structural change 2}
- [ ] Run characterization tests after each change
- [ ] Run full test suite
- [ ] Commit: "Refactor {scope}: {description} — no behavior change"

## Phase C — Verify

- [ ] Characterization tests still pass
- [ ] Full test suite passes
- [ ] `/review-work` returns pass
- [ ] Diff reviewed for accidental behavior changes

---

## Behavior quirks found during characterization

(Add entries here if you find what looks like a bug while writing tests. Do NOT fix them in this refactor — file separately.)

- **{quirk 1}:** {description}. Captured in test X. **Proposed follow-up:** {bug fix Hodos run / confirm as intended}.

---

## Out of scope

- **Behavior changes** — this is refactor only. No new features, no bug fixes.
- **Unrelated cleanups** — don't "while I'm in here" other files unless explicitly scoped above.

---

## Acceptance criteria

- All characterization tests pass after refactor
- Full test suite still passes
- No user-visible change (verify with `/review-work`)
- Code is measurably cleaner (specific metric: fewer lines / less duplication / better names / whatever applies)
```

## Rules

1. **No behavior change during refactor.** Period. Not even small ones. Not even "obvious bugs."
2. **Characterization tests are non-negotiable.** Without them you cannot prove the refactor is safe.
3. **Commit characterization tests separately** from the refactor. Two commits: "add tests" then "refactor." This lets you bisect if something breaks.
4. **Small steps.** A 500-line refactor diff with one "refactor done" commit is not reviewable. Break it up.
5. **Quirks get noted, not fixed.** During refactor, suspected bugs get logged as follow-up Hodos tasks.
6. **Run the full suite.** Characterization tests prove your refactor didn't break your target. The full suite proves you didn't break something else.

## What you do NOT do in this skill

- Do not fix bugs you notice during the refactor
- Do not add features, even small ones
- Do not rename things that aren't in scope
- Do not skip characterization tests (even for "simple" refactors — those are where the silent bugs hide)
- Do not proceed to refactor if characterization tests reveal unexpected behavior — stop, discuss with user
