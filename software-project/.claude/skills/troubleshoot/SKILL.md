---
name: troubleshoot
description: "Use this when something is broken — wrong output, crash, unexpected behavior, failing test. Runs a four-phase root-cause investigation (reproduce → pattern → hypothesize → fix). Documents everything in the notebook. Stops after 3 failed fix attempts and escalates rather than thrashing. Formerly systematic-debug."
---

# troubleshoot

Find the root cause of a bug through a structured four-phase investigation, not by guessing. Document everything in the notebook so future sessions can pick up where this one left off.

## When this skill fires

Auto-fires on:

- *"Troubleshoot X"* / *"Debug X"*
- *"Why is X failing"* / *"Why isn't X working"*
- *"What's broken"* / *"Something's broken"*
- *"X isn't working"* / *"X is broken"*
- *"Find the bug"* / *"There's a bug in X"*
- *"This doesn't work"* / *"This is wrong"*
- *"Why is this value wrong?"*
- *"It's giving the wrong result"*
- *"It's crashing"* / *"It keeps failing"*
- *"This used to work and now it doesn't"*
- *"What happened to X?"*
- *"Figure out why X"*
- *"The test is failing"* / *"Tests are broken"*

Slash override: `/troubleshoot` (file: `commands/troubleshoot.md`).

## What this skill does

Four phases, in order:

1. **Reproduce** — find the minimal sequence that reliably triggers the failure. If it can't be reproduced, it can't be fixed.
2. **Pattern** — look at the failure surface. What's the actual error? What was the input? What was expected? Check git log, related code, specs.
3. **Hypothesize** — propose 1-3 specific causes. Each hypothesis must be testable.
4. **Assess and fix** — if the root cause is a simple mistake (code is wrong, spec is right), fix it directly. If the root cause is systematic (spec is wrong or missing), document it and flow into `brainstorm` → main workflow.

Documents everything in `notebook/{YYYY-MM-DD}-troubleshoot-{name}.md` — what was observed, tried, learned, fixed, and what's still uncertain. This is the persistent record for recurring problems.

Stops after **3 failed fix attempts** and escalates to the user with a summary, rather than thrashing.

## Hard rule — root cause, not symptom

The fix must address the actual cause, not just suppress the symptom. If the symptom is a null pointer exception, "add a null check" is only the right fix if the null is *expected* in that context. If the null is unexpected, the real fix is upstream.

## Full instructions

Complete behavior contract — phase templates, what counts as a hypothesis, escalation criteria, notebook requirements — in [`../../commands/troubleshoot.md`](../../commands/troubleshoot.md).

## Related skills

- If the bug reveals missing test coverage or spec/code disagreement: hand off to `analyze` for full health check
- If the fix requires significant code changes: route through the main workflow (brainstorm → write-spec → write-plan → execute-plan)
