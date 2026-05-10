---
name: systematic-debug
description: "Use this when something is broken — wrong output, crash, unexpected behavior, failing test. Runs a four-phase root-cause investigation (reproduce → pattern → hypothesize → fix). Stops after 3 failed fix attempts and escalates rather than thrashing. NOT for refactoring or adding tests to working code."
---

# systematic-debug

Find the root cause of a bug through a structured four-phase investigation, not by guessing.

## When this skill fires

Auto-fires on:

- *"Let's debug this"*
- *"Why is X failing"*
- *"Find the bug"*
- *"What's broken"*
- *"X isn't working"*

Slash override: `/debug` (file: `commands/debug.md`).

## What this skill does

Four phases, in order:

1. **Reproduce** — find the minimal sequence that reliably triggers the failure. If it can't be reproduced, it can't be debugged.
2. **Pattern** — look at the failure surface. What's the actual error? What's the stack trace? What was the input? What was the expected output?
3. **Hypothesize** — propose 1-3 specific causes. Each hypothesis must be testable (i.e., "if hypothesis A is right, then doing X should change the failure in way Y").
4. **Fix** — test the hypothesis. If it's the cause, fix it. If not, drop it and try the next hypothesis.

Stops after **3 failed fix attempts** and escalates to the user with a summary, rather than thrashing.

## Hard rule — root cause, not symptom

The fix must address the actual cause, not just suppress the symptom. If the symptom is a null pointer exception, "add a null check" is only the right fix if the null is *expected* in that context. If the null is unexpected, the real fix is upstream.

## Full instructions

Complete behavior contract — phase templates, what counts as a hypothesis, escalation criteria — in [`../../commands/debug.md`](../../commands/debug.md).

## Related skills

- After fix is verified: hand off to `verification-before-completion`
- If the bug reveals missing test coverage or spec/code disagreement: hand off to `audit-subsystem` for full health check
