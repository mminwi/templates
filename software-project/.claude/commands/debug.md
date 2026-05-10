---
description: Systematic debugging — reproduce, pattern, hypothesize, fix. Forces a rethink after 3 failed attempts instead of letting the AI try random fixes
---

# /debug

## When to use

Invoke when something is broken and you need to fix it — a failing test, a user-reported bug, a wrong output, a crash.

Do **not** use for feature work (that's full Hodos) or for performance issues that aren't bugs (that's `/profile`).

## The four-phase debug discipline

Debugging has a specific failure mode: AI (or a tired human) tries random fixes until something seems to work, then declares victory. The fix "works" because it masks the symptom, but the root cause is still there. Next week, the bug returns in a different form.

This skill prevents that. Four phases, in order. You cannot skip phases. **You stop after 3 failed fix attempts and escalate to architectural review.**

### Phase 1 — Reproduce

Produce an exact, repeatable sequence that makes the bug happen. Written down.

- **Input:** what data / state / user action triggers the bug
- **Expected behavior:** what should happen
- **Actual behavior:** what happens instead
- **Repeatability:** always / sometimes / once-seen
- **Environment:** which version, which data, which user role

**If you cannot reproduce the bug, stop.** Do not try to fix something you cannot trigger. Report to the user and ask for more information. Fixing an unreproduced bug is guessing.

### Phase 2 — Pattern analysis

Before hypothesizing, look for patterns. One bug is often the tip.

- Search the codebase for similar code (same function pattern, same data flow). Are other places affected?
- Check recent changes. `git log` on the relevant files. Did the bug start after a specific commit?
- Check related specs. Does the current behavior match what the spec says? (If spec says behavior is correct and code disagrees, that's a different kind of bug than if the spec is silent.)
- Check if this bug class has happened before. Search commit messages for similar fixes.

Output: a list of related locations and recent changes.

### Phase 3 — Hypothesize and test

Form specific, testable hypotheses. For each one:

1. **Hypothesis:** one sentence. "The bug is caused by X."
2. **How to test:** a specific check that distinguishes this hypothesis from others.
3. **Evidence for:** what you observed that supports it.
4. **Evidence against:** what you observed that contradicts it.
5. **Verdict:** supported / rejected / inconclusive.

Do at least three hypotheses. One plausible hypothesis is selection bias — you're just anchoring on the first thing.

Test hypotheses with read-only actions where possible. Logging, adding print statements, stepping through. **Do not attempt a fix during this phase.**

### Phase 4 — Fix

Only after a hypothesis is supported by clear evidence.

- **Fix the root cause.** Not the symptom. "Add a null check" is usually a symptom fix. "Return empty list instead of None from function X" may be the root cause.
- **Write a regression test FIRST.** Commit the failing test. Then commit the fix. The failing-test commit proves you actually understand the bug; the passing-test commit proves you fixed it.
- **Check for the bug's siblings.** If the pattern analysis found related code, fix those too (or document why they're safe).
- **Update the relevant spec.** If the bug was caused by spec drift, fix the spec alongside the code.

## The 3-attempt rule

If you've tried 3 different fixes and the bug is still present, **stop.** Do not try a 4th.

At this point:
1. Return to the user with a report: what you tried, what happened, what you still don't understand.
2. Consider that the bug might be architectural, not local. A 4th local attempt will not find an architectural cause.
3. Propose one of:
   - A call with the user to walk through the code together
   - A fresh-context review of your debug file to find what you've missed
   - A wider investigation — is the assumption about how a whole subsystem works actually correct?

This rule exists because "try random fixes until one works" is how bugs get papered over instead of fixed. Respect the rule.

## Output format

Write to `plans/{YYYY-MM-DD}-bug-{short-name}.md`.

```markdown
# Bug Investigation — {short name}

**Date:** {YYYY-MM-DD}
**Reporter:** {who found it}
**Severity:** Critical / Major / Minor
**Status:** Investigating / Root-cause identified / Fixed / Stalled (3-attempt rule)

---

## Phase 1 — Reproduction

**Steps to reproduce:**
1. {step}
2. {step}

**Expected:** {what should happen}
**Actual:** {what happens}
**Repeatable:** always / sometimes / once

**Environment:** {version, data, user}

---

## Phase 2 — Pattern analysis

**Related code locations checked:**
- {file:line} — {relevance}

**Recent changes examined:**
- {commit hash / date} — {summary}

**Specs checked:**
- {spec file} — {relevant section, matches behavior? yes/no}

**Similar past bugs:**
- {commit or ticket, if any}

---

## Phase 3 — Hypotheses

### Hypothesis 1: {one sentence}
- **Test:** {specific check}
- **Evidence for:** {observations}
- **Evidence against:** {observations}
- **Verdict:** supported / rejected / inconclusive

### Hypothesis 2: ...

### Hypothesis 3: ...

---

## Phase 4 — Fix

**Root cause:** {clear one-paragraph explanation}
**Why the symptom appeared:** {chain from root cause to observed behavior}

**Fix:**
- {file:line} — {what changed}
- {file:line} — {what changed}

**Regression test:**
- {test file:line} — commits the scenario from Phase 1

**Siblings checked:**
- {list of related locations — fixed? safe? why?}

**Spec updates:**
- {spec file} — {what was updated}

---

## Fix attempts (running count)

- [x] Attempt 1: {what was tried} → {outcome}
- [x] Attempt 2: {what was tried} → {outcome}
- [ ] Attempt 3: {pending}

**If 3 attempts all fail: STOP. Escalate.**
```

## Rules

1. **No fix without reproduction.** Phase 1 is not optional.
2. **No fix without a hypothesis supported by evidence.** Guessing is not allowed.
3. **Write the failing test before the fix.** This proves you understand the bug.
4. **Count your attempts.** Three strikes means stop and escalate.
5. **Root cause, not symptom.** If you're adding a special case, ask yourself if you really understand why the general case is wrong.
6. **Update the spec if spec drift contributed.** Code and spec out of sync is itself a bug.

## What you do NOT do in this skill

- Do not "just try" fixes to see what works
- Do not fix the symptom and move on
- Do not skip the regression test
- Do not silently continue past 3 failed attempts
- Do not declare a fix "working" without a passing regression test
