---
description: Execute an approved plan task by task, checking off items as they complete, stopping for deviations rather than improvising
---

# /execute-plan

## When to use

Invoke after:
1. The plan has been written (`/write-plan`) and approved.
2. The critic has reviewed (`/critic-review`) and findings are resolved.
3. The skeleton has been written (`/skeleton-first`) and approved.

This skill fills in the skeleton, task by task, against the plan. It is the last step before `/review-work`.

## The contract

**The plan is authoritative.** You follow it. You do not improvise. If the plan is wrong, you stop and fix the plan — you do not quietly do something different.

This is the discipline that prevents AI drift on long tasks. The checkbox plan is your memory across context compaction. The plan is truth.

## Workflow

### Starting

1. Read the plan file from `.claude/plans/{plan-name}.md`.
2. Confirm status is `Approved`. If it's `Draft`, stop and ask the user to approve or run `/critic-review`.
3. Change status to `In progress`. Record the start time.
4. Review the task list. Identify the next unchecked task.

### Per task

For each task, in order:

1. **Read the task.** Understand the file, the action, and the done criterion.
2. **Read the file being modified.** Don't work from memory.
3. **Perform the specified change.** Only what the task says. Nothing extra.
4. **Verify against the "done when" criterion.** If the criterion is testable, test it.
5. **Check the box.** Update the plan file: `- [ ]` becomes `- [x]`.
6. **Note anything unusual.** If the task took longer than expected, failed, or revealed something, add a note under the task in the plan file.
7. **Proceed to the next task.**

### Stopping conditions

Stop immediately if:

- A task can't be completed as written (file not found, precondition missing, unclear action)
- Completing the task would require changes outside the "Files touched" table
- A task that looked simple reveals an issue the plan did not anticipate
- An existing test fails due to your changes
- The user interrupts

**Do not improvise past these stops.** Report to the user. Propose a plan revision if needed. Wait for approval.

### Resuming after an interruption

Plans are resumable. If the session ends mid-execution (context fills up, user closes the window, crash), the next session:

1. Reads the plan file.
2. Finds the first unchecked task.
3. Resumes from there.

This is why plan files matter. They are not documentation — they are execution state.

## Rules

1. **Check boxes in real time.** Not at the end. If you finish task 3, check box 3 *before* starting task 4. The file is state.

2. **One task at a time.** Don't batch. Don't plan ahead. Execute the current task, verify, check, move on.

3. **Deviations = stop.** If you notice the plan should have said X instead of Y, stop. Tell the user. Let them decide whether to revise the plan or accept the deviation. Never silently do something different from the plan.

4. **Log, don't hide.** If a task took longer than expected, note it. If a task surprised you, note it. If the approach changed, update the plan AND note the change. The plan is a decision log — preserve the decisions.

5. **Tests get written when the plan says.** If Phase C of the plan says "run tests," you run tests. You don't skip. You don't hope.

6. **Gherkin scenarios are the acceptance criteria.** When you think you're done, the scenarios must pass. If they don't, you're not done — even if all checkboxes are ticked.

## Plan status transitions during execution

- `Approved` → `In progress` at start
- `In progress` → `Complete` only after:
  - All checkboxes ticked
  - All Gherkin scenarios pass
  - `/review-work` has returned pass or pass-with-notes
  - User has accepted
- `In progress` → `Superseded` if the plan is abandoned or rewritten

## What to output during execution

Brief updates at key moments:

- When starting: "Executing plan `{name}`. {N} tasks. Starting task 1."
- When completing a task: (just check the box silently unless the task was notable)
- When blocked: "Stopped at task {N}: {reason}. Recommending: {plan revision / user input needed}."
- When finishing: "All tasks complete. Plan status: ready for `/review-work`."

Do not narrate every single task verbally. The checkbox changes are the narration.

## Output handoff

After the last task is checked:

1. Run the final verification tasks (usually last phase of the plan)
2. Change plan status to a candidate state pending review
3. Invoke `/review-work` automatically, or prompt the user to invoke it

## What you do NOT do in this skill

- Do not write a new plan — that's `/write-plan`
- Do not skip `/review-work` — Phase 4 is required
- Do not modify files not listed in the plan
- Do not "clean up" code you happen to see that's outside scope
- Do not batch check boxes at the end — real-time updates only
- Do not proceed past a stopping condition without user approval
