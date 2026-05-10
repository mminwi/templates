---
name: verification-before-completion
description: "Compatibility/standalone verification pass. Normally folded into execute-plan. Use before any done/fixed/complete claim, especially for small work without a plan."
---

# verification-before-completion

The gate before any "done" claim. For planned work, this is inside `execute-plan`; for small unplanned work, use this standalone.

## When this skill fires

**Auto-fires whenever the AI is about to say:**

- *"That's done"*
- *"All set"*
- *"Implemented"*
- *"Fixed"*
- *"This should work now"*
- *"Let me know if anything else needs to change"*

The trigger is the *"done"* word being about to be said, not the user requesting verification.

User-driven trigger phrases (when explicit verification is requested):

- *"Verify it works"*
- *"Did this actually work?"*
- *"Check before declaring done"*

## What this skill does

- Enumerates verification claims as concrete bullet points (each one testable)
- Picks a verification method per claim:
  - **Manual / dev server** for UI changes
  - **Curl / API call** for endpoint changes
  - **DB query** for data writes
  - **Existing test run** when a test already covers the behavior
  - **Code re-read** when the change has no runtime effect
- Runs the checks
- Produces a verification report (table format) with ✅ / ⚠️ / ❌ per claim
- If anything fails: does NOT declare done; routes back to `execute-plan` (fix it) or surfaces to the user
- If anything is unverifiable: declares done only with explicit user acceptance

## Hard rule — no silent done

Every "done" claim carries a verification report or a one-line note explaining why no verification was possible. Saying "done" without verification is the failure mode this skill exists to prevent.

## Common anti-patterns

- *"It looks right"* — doesn't count
- *"The types check"* — necessary, not sufficient
- *"All existing tests pass"* — existing tests don't cover the new behavior
- *"I added a test"* — did the test actually exercise the new behavior, or pass trivially?

## Full instructions

Complete behavior contract — verification report template, anti-patterns, when to handoff back to building — in [`../../commands/verify.md`](../../commands/verify.md).

## Related skills

- Included inside `execute-plan` for planned work
- Can be used standalone for Tier 0/1 work
- For new tests, hands off to `backfill-tests` if a nontrivial test is needed
