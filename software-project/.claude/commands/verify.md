---
description: Verify behavior with concrete checks before claiming work is complete. Fires automatically before declaring "done" — does the change actually do what was intended, not just compile.
---

# /verify

## When to use

Invoke (and the AI should auto-fire this) **before** any "done" / "fixed" / "implemented" / "complete" claim. The auto-trigger is the existence of any of those words about to be said by the AI.

Trigger phrases (auto-fires when the AI is about to say):

- *"That's done"* / *"All set"* / *"Implemented"* / *"Fixed"*
- *"This should work now"*
- *"Let me know if anything else needs to change"*

If the user is explicitly asking for verification:

- *"Verify it works"* / *"Did this actually work?"* / *"Check before declaring done"*

Do **not** use for:

- Pure information-gathering tasks (no behavior change to verify)
- Documentation-only changes (verify with a re-read instead)

## Core discipline — "compiles" is not "verified"

Far too many "done" claims rest on a successful build or a typecheck. Those are necessary but not sufficient. Verification answers:

> *Does the change actually produce the intended behavior in the running system?*

Not: did the code compile. Not: did the type checker pass. Not: do the tests still run. **Does the thing actually work the way the spec / plan / user intent says it should?**

## Process

### Step 1 — Enumerate verification claims

List, as bullet points, the specific behaviors the change is supposed to produce. Be concrete — each item should be testable in some way.

For example, after implementing a "delete account" endpoint, the verification claims might be:

- A POST to `/api/account/delete` with valid auth returns 204
- The user's row is marked `deleted = true` in the database
- The user's API tokens are revoked
- The user's order history is preserved (per spec rule)
- The user can no longer log in with the deleted account

### Step 2 — Identify how to check each claim

For each claim, decide the verification method:

- **Manual / via dev server** — when the change has a UI surface, hit it and observe
- **Curl / API test** — when the change is an endpoint, call it and inspect the response
- **DB query** — when the change writes data, `psql` (or equivalent) and check
- **Existing test run** — when there's already a test that covers this behavior, run it
- **New test** — when there's no test and the change merits one, write it (handoff to `/backfill-tests` if nontrivial)
- **Code re-read** — when the change is truly pure (no runtime effect), the verification is reading the diff against the spec

If a claim can't be verified with any of the above, that's a flag — say so. "Pure code change, no runtime test possible" is honest. "Looks right" is not verification.

### Step 3 — Run the checks

Execute the verification methods. Capture the output. Be specific about what passed and what didn't.

### Step 4 — Report

Produce a verification report (or, for trivial changes, just a one-line statement):

```markdown
## Verification — {what was done}

| Claim | Method | Result |
|---|---|---|
| {claim 1} | {curl / DB / test / re-read} | ✅ Pass — {evidence} |
| {claim 2} | ... | ⚠️ Could not verify — {reason} |
| {claim 3} | ... | ❌ Failed — {what happened} |
```

If everything passes: declare done.

If anything fails: do **not** declare done. Either fix the issue (route back to `/execute-plan`) or surface the failure to the user with options.

If anything could not be verified: declare done only if the user explicitly accepts the unverified items. Default is "not done."

## Rules

1. **No silent "done" claims.** Every "done" carries a verification report or a one-line note explaining why no verification was possible.
2. **Compiles is not verified.** Build success is necessary but not sufficient.
3. **Tests passing is not always verified.** Tests can be wrong, can mock the very thing being tested, or can be unrelated to the actual change. Read the test, confirm it tests the claim.
4. **One claim, one method.** Don't claim "everything works" without enumerating what "everything" means.
5. **Failures route back to building, not to denial.** If verification fails, the answer is "fix it," not "ignore it."
6. **The user reads the report.** The change manifest at end-of-turn references the verification report for any completed work.

## Common verification anti-patterns

- *"It looks right."* (Doesn't count. Read the spec, run the thing.)
- *"The types check."* (Necessary, not sufficient.)
- *"All the existing tests still pass."* (Existing tests don't cover the new behavior.)
- *"I added a test."* (Did the test actually exercise the new behavior? Or did it pass trivially?)
- *"The diff looks clean."* (Verification is about behavior, not aesthetics.)

## What you do NOT do in this skill

- Do not write new tests as part of verification (handoff to `/backfill-tests` if a test would be nontrivial to write)
- Do not refactor or clean up while verifying (handoff to `/refactor` if cleanup is needed)
- Do not declare done without running the verification methods
- Do not skip claims because they "look obvious"
