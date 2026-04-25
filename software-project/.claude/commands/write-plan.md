---
description: Produce a numbered checkbox plan file for non-trivial work — 2-5 minute tasks, file paths, done criteria, resumable across sessions
---

# /write-plan

## When to use

Invoke after the design artifacts exist (decomposition, page specs, event specs, flowcharts, Gherkin) and before any code is written. The plan is the final Phase 1 artifact — it's what gets handed to `/critic-review` in Phase 2, and what `/execute-plan` follows in Phase 3.

Also invoke for smaller tasks where the design artifacts are unnecessary but a plan is still warranted (bug fixes that touch multiple files, refactors, spec updates).

## What you must produce

A plan file at `.claude/plans/{YYYY-MM-DD}-{short-task-name}.md`. Use today's date and a short hyphenated name derived from the task (e.g., `2026-04-24-verified-customer-tag.md`).

## File template

```markdown
# Plan — {Task name}

**Date:** {YYYY-MM-DD}
**Author:** Claude + {user name}
**Status:** Draft / Approved / In progress / Complete / Superseded
**Related artifacts:**
- Decomposition: {link, if exists}
- Page specs: {links}
- Event specs: {links}
- Gherkin: {links}

---

## Goal

One paragraph. What success looks like. This is the thing the user signed off on.

---

## Out of scope

Explicit exclusions. Things that might seem related but are not part of this plan.

- {thing 1}
- {thing 2}

---

## Files touched

Every file this plan reads, creates, modifies, or deletes. No surprises later.

| File | Action | Why |
|---|---|---|
| `path/to/file.py` | Modify | Add new function `foo()` |
| `path/to/new-file.md` | Create | New page spec |
| `path/to/old.json` | Delete | Replaced by `new.json` |

---

## Tasks

Numbered checkboxes. Each task is 2-5 minutes of focused work. No task should be so vague that "did I do it?" is unclear. No task should be so big it needs sub-tasks — split it.

- [ ] **1. {Short imperative description}**
  - File: `path/to/file.py`
  - Action: {specific change}
  - Done when: {observable criterion}

- [ ] **2. {Next task}**
  - File: ...
  - Action: ...
  - Done when: ...

- [ ] **3. ...**

Group tasks by logical phase if the plan is long:

### Phase A — Preparation
- [ ] 1. ...
- [ ] 2. ...

### Phase B — Implementation
- [ ] 3. ...
- [ ] 4. ...

### Phase C — Verification
- [ ] 5. Run tests: `{command}`
  - Done when: all tests pass including the new Gherkin scenarios
- [ ] 6. Invoke `/review-work` on the diff
  - Done when: reviewer returns pass or pass-with-notes

---

## Acceptance criteria

How the user confirms the plan is complete. Usually the Gherkin scenarios from Phase 1, plus any manual checks.

- All Gherkin scenarios in [spec/events/{event}.md](...) pass
- Manual check: {specific user-observable behavior}
- No regressions in {related tests}

---

## Risks and unknowns

Things that could cause this plan to change mid-execution. Flag them up front so the user isn't surprised.

- **Risk:** {description} — **Mitigation:** {how to handle}
- **Unknown:** {thing you're not sure about} — **How to resolve:** {ask user, prototype, research}

---

## Notes

Any context that will help future-you or the next AI session understand this plan. Commit hashes, related issues, decisions made during design, etc.
```

## Rules

1. **Tasks must be 2-5 minutes.** If a task reads as "implement X feature" and you'd struggle to say what "done" means after 5 minutes, it's too big. Split it. A plan with 30 small tasks is better than a plan with 5 big ones — the small plan is resumable, the big one is not.

2. **Every task names a file and a criterion.** "Add validation" is not a task. "Add `validate_email()` to `utils/validation.py`, done when it returns False for missing `@`" is a task.

3. **Out of scope is load-bearing.** The #1 cause of plan failure is scope creep. Writing down exclusions up front prevents the "while we're in there..." spiral.

4. **Status field is a real state machine.**
   - `Draft` — writing the plan
   - `Approved` — user signed off; implementation can start
   - `In progress` — execution underway; checkboxes being ticked
   - `Complete` — all checkboxes ticked, reviewed, work committed
   - `Superseded` — plan was abandoned or replaced; link to the replacement

5. **Never delete an old plan.** Even abandoned plans are decision history. Mark them `Superseded` and explain why.

6. **Commit the plan.** The plan file goes into git with the work it describes. Plans are documentation.

## Output handoff

After the plan is written:

1. Set status to `Draft`
2. Present to user for review
3. On user approval, change status to `Approved`
4. User invokes `/critic-review` (Phase 2) before execution begins
5. Only after critic review passes do you proceed to `/skeleton-first` / `/execute-plan` (Phase 3)

## What you do NOT do in this skill

- Do not start coding — this is planning only
- Do not skip the "files touched" table — surprises come from missed files
- Do not write tasks without "done when" criteria
- Do not produce a plan for work that genuinely is trivial — just do the work
