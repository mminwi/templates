---
description: Produce a checkbox implementation plan from aligned spec canvases; includes notebook link, test plan updates, skeleton phase when substantial, and automatic critic loop
---

# /write-plan

## When to use

Invoke after the relevant spec canvases are aligned and before implementation begins.

Use for:

- Tier 2 and Tier 3 implementation work
- Any change touching several files
- Any multi-agent or sequenced-agent work
- Refactors/debug fixes substantial enough to need execution state
- Any work where the user asks for a plan

Do not use for tiny edits or small single-file fixes unless the user explicitly wants a plan.

## What you must produce

Produce or update all three:

- Plan file: `plans/{YYYY-MM-DD}-{short-task-name}.md`
- Notebook entry: `notebook/{YYYY-MM-DD}-{short-topic}.md`
- Test plan: `tests/TEST_PLAN.md`

The notebook gets an overview, not the full plan text.

## File template

```markdown
# Plan — {Task name}

**Date:** {YYYY-MM-DD}
**Author:** Claude + {user name}
**Status:** Draft / Approved / In progress / Complete / Superseded
**Related artifacts:**
- Specs/canvases: {links}
- Notebook: {link}
- Test plan: `tests/TEST_PLAN.md`
- Critic report: {link if produced}

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

## Execution model

| Slice/Phase | Owner | Execution Mode | Writable Files | Interfaces |
|---|---|---|---|---|
| Phase A | Composer / agent name | parallel / series | `path` | `interface` |

Rules:

- No two agents modify the same file in parallel.
- Shared-file slices may be decomposed but must run in series.
- Composer owns integration and small glue changes.

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

Group tasks by logical phase. For substantial work, include skeleton/framework and skeleton review phases:

### Phase A — Preparation
- [ ] 1. ...
- [ ] 2. ...

### Phase B — Skeleton / Framework
- [ ] 3. Create file/module/page structure
  - File: ...
  - Done when: structure, signatures, contracts, and placeholders exist without full logic
- [ ] 4. Review skeleton shape against specs
  - Done when: separate reviewer/composer confirms the structure matches the spec canvases

### Phase C — Implementation
- [ ] 3. ...
- [ ] 4. ...

### Phase D — Tests / Verification
- [ ] 5. Update `tests/TEST_PLAN.md`
  - Done when: new checks, regression checks, manual checks, and known gaps are recorded
- [ ] 6. Add or update tests
  - Done when: tests exist at the named paths
- [ ] 7. Run tests/manual checks
  - Done when: results are recorded
- [ ] 8. Separate-agent reviewer checks completion
  - Done when: reviewer verifies the plan was executed or gaps are fixed/escalated
- [ ] 9. Update notebook result/conclusion
  - Done when: result, surprises, and follow-up are recorded

---

## Acceptance criteria

How completion is verified against specs/canvases and tests.

- Relevant CSVs/mock-ups/interaction matrices are implemented
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

## Automatic critic loop

Critic review belongs inside `write-plan`. The user should not need to request it separately.

Run critic review automatically for most non-trivial work:

```text
write-plan
  → critic-review
  → revise plan/specs if needed
  → critic-review again if substantive changes were made
  → repeat at most two critic/revision attempts
  → ask user only if still blocked, conflicted, or after two unresolved iterations
```

If escalating to the user, provide:

```text
Issue:
What we tried:
Why it is still unresolved:
Options:
Recommendation:
Consequence of recommendation:
```

## Test plan format

Maintain `tests/TEST_PLAN.md` with:

- Purpose
- How to run the test suite
- Current regression suite
- Current change additions
- Manual verification checks
- Known gaps

Tests are cumulative. Do not remove regression checks just because the current plan is complete.

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

6. **Planning engages notebook and test plan.** If work needs a plan, it also needs notebook context and test-plan coverage.

7. **Skeleton is part of the plan.** Do not make skeleton/framework-first a separate user-called workflow. Include it as a phase when work is substantial.

8. **One strong recommendation.** If there is one best path, recommend it. Do not pad with weak options.

## Output handoff

After the plan is written:

1. Set status to `Draft`
2. Ensure notebook and test plan were created/updated
3. Run automatic critic loop if required
4. Present the approved/revised plan summary and recommendation
5. On user approval, change status to `Approved`
6. Hand off to `/execute-plan`

## What you do NOT do in this skill

- Do not start coding — this is planning only
- Do not skip the "files touched" table — surprises come from missed files
- Do not write tasks without "done when" criteria
- Do not produce a plan for work that genuinely is trivial — just do the work
