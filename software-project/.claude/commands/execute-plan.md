---
description: Execute an approved plan task by task, including skeleton phases, tests, notebook closeout, and mandatory separate-agent verification before completion
---

# /execute-plan

## When to use

Invoke after:
1. The plan has been written (`/write-plan`) and approved.
2. The plan's critic loop has resolved or been user-approved.
3. The plan names the specs/canvases, notebook, test plan, files touched, and execution model.

This skill executes the approved plan. Skeleton/framework work, implementation, tests, notebook updates, and final verification are plan phases, not separate user-called workflows.

## The contract

**The plan is authoritative.** You follow it. You do not improvise. If the plan is wrong, you stop and fix the plan — you do not quietly do something different.

This is the discipline that prevents AI drift on long tasks. The checkbox plan is your memory across context compaction. The plan is truth.

## Workflow

### Starting

1. Read the plan file from `plans/{plan-name}.md`.
2. Confirm status is `Approved`. If it's `Draft`, stop and ask for approval or plan revision.
3. Change status to `In progress`. Record the start time.
4. Read linked spec canvases, notebook entry, and `tests/TEST_PLAN.md`.
5. Review the execution model: owners, writable files, interfaces, parallel/series decisions.
6. Identify the next unchecked task.

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
- A parallel/series ownership rule would be violated
- The implementation no longer matches the spec canvases

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

5. **Tests get written when the plan says.** If the plan says update tests or `tests/TEST_PLAN.md`, do it. Don't skip. Don't hope.

6. **Spec canvases and test plan are acceptance criteria.** CSVs, mock-ups, interaction matrices, state tables, and test plan entries must be satisfied. If they aren't, you're not done — even if all checkboxes are ticked.

7. **Notebook closes the loop.** Update the notebook with results, surprises, unresolved gaps, and follow-up before marking the plan complete.

## Plan status transitions during execution

- `Approved` → `In progress` at start
- `In progress` → `Complete` only after:
  - All checkboxes ticked
  - Required tests/manual checks run or documented
  - `tests/TEST_PLAN.md` updated
  - Notebook updated
  - Separate-agent reviewer verifies the work against plan/spec/test plan
  - User-approved exception for any unresolved gap
- `In progress` → `Superseded` if the plan is abandoned or rewritten

## What to output during execution

Brief updates at key moments:

- When starting: "Executing plan `{name}`. {N} tasks. Starting task 1."
- When completing a task: (just check the box silently unless the task was notable)
- When blocked: "Stopped at task {N}: {reason}. Recommending: {plan revision / user input needed}."
- When finishing implementation: "All tasks complete. Starting separate-agent verification."
- When verification passes: "Plan complete. Reviewer verified plan/spec/test alignment."

Do not narrate every single task verbally. The checkbox changes are the narration.

## Output handoff

After the last implementation task is checked:

1. Run the final verification tasks (usually last phase of the plan)
2. Confirm notebook and test plan are updated
3. Invoke a separate reviewer/fresh context to verify plan completion
4. If reviewer finds gaps, fix and re-check at most two times
5. Escalate to orchestrator/composer if still unresolved
6. Mark plan `Complete` only after pass or user-approved exception

## Separate-agent verification

The reviewer asks:

```text
This is what the agent was told to do. Did it actually do it?
```

Reviewer input should be short and focused:

- Plan file
- Relevant spec canvases
- Files changed
- Test plan entries
- Notebook result section

Reviewer checks:

- Every plan checkbox is complete and backed by actual file changes.
- Implementation matches CSVs/mock-ups/interaction matrices/state tables.
- Skeleton/framework structure was respected if required.
- Required tests were added or updated.
- Test plan was run or remaining manual checks are explicit.
- No planned work was silently skipped.
- No unplanned behavior/files were introduced without explanation.
- Notebook was updated.

Failure loop:

```text
reviewer identifies gaps
  → builder fixes gaps
  → reviewer re-checks
  → repeat at most two fix/review attempts
  → escalate to orchestrator/composer if still unresolved
```

## What you do NOT do in this skill

- Do not write a new plan — that's `/write-plan`
- Do not skip separate-agent verification
- Do not modify files not listed in the plan
- Do not "clean up" code you happen to see that's outside scope
- Do not batch check boxes at the end — real-time updates only
- Do not proceed past a stopping condition without user approval
