---
name: execute-plan
description: "Use this AFTER a plan is approved (and skeleton if Big-tier). Implements the plan checkbox by checkbox, with NO improvising. If the plan is wrong, stop and revise the plan — don't drift from it. Hands off to verification-before-completion before declaring done."
---

# execute-plan

Implement an approved plan, step by step, without deviation. If the plan turns out to be wrong, stop and revise the plan — don't improvise off-plan.

## When this skill fires

Auto-fires on:

- *"Execute the plan"*
- *"Build it"*
- *"Run the plan"*
- *"Implement this"*

Or after `write-plan` is approved and (for Big-tier work) after `skeleton-first` is approved.

## What this skill does

- Reads the approved plan from `.claude/plans/{YYYY-MM-DD}-{task}.md`
- Works through each checkbox in order
- Marks each item complete as it's done (in the plan file or in conversation)
- Writes code that matches the plan's file paths and completion criteria
- For Big-tier work, runs against the approved skeleton from `skeleton-first`
- Hands off to `verification-before-completion` before any "done" claim

## Hard rule — plan is contract

The plan is the contract. If the plan is wrong, the answer is **stop and revise the plan first, then resume execution**. Drifting off-plan mid-execution is the #1 anti-pattern Hodos exists to prevent.

If something during execution reveals the plan is incomplete (missing step, wrong order, unclear criterion), surface that to the user and propose a plan revision before continuing.

## Full instructions

Complete behavior contract — checkbox tracking, plan-revision flow, what counts as "drift," handoff to verification — in [`../../commands/execute-plan.md`](../../commands/execute-plan.md).

## Related skills

- Inputs: approved `write-plan` output, approved `skeleton-first` output (Big-tier)
- Output handoff: `verification-before-completion` (always — never declare done without verifying)
- Then `review-work` (audit the diff against the plan, ideally in a separate context)
