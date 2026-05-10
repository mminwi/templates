---
description: Full health check on one subsystem — code hygiene, spec drift, test coverage, ops/security — using 4 parallel agents. Produces a punch list and test plan recommendations.
---

# /analyze

> Formerly `/audit-subsystem`. Same skill, shorter name.

## When to use

Invoke when:
- You haven't touched a subsystem in a while and want to know its health
- Before a significant change to a subsystem — know what debt you're inheriting
- Periodically (monthly or quarterly) as a maintenance practice
- Before adding new features to an older subsystem
- After a project takeover or onboarding
- You want to check if specs match reality
- You want to know if test coverage is adequate and whether tests should be added

Trigger phrases:
- *"Audit X," "how healthy is X," "check the health of X"*
- *"Check for spec drift," "is the spec still accurate," "scan the specs"*
- *"Does X have enough test coverage," "what tests exist for X"*

Scope this to **one subsystem at a time** — a folder, a module, or a feature area. Do not try to audit the whole project in one run.

## The parallel-subagent pattern

This skill uses **four Explore subagents running concurrently**. Each agent has a narrow brief and does not share context with the others. This is important:

- **Narrow brief = better audit.** An agent asked to do one thing produces more thorough findings than an agent asked to cover 10 categories at once.
- **No shared context = no groupthink.** Each agent sees the code with fresh eyes. Overlapping findings across agents are a signal that the issue is real.
- **Concurrent = fast.** Four agents in parallel finish in the time it would take one sequential agent to get through its first two categories.

You (as Claude orchestrating the skill) launch all four agents in a **single message with multiple tool calls** so they execute concurrently. Then you merge and dedupe their reports into a single punch list.

## Step 1 — Identify the subsystem and its paths

Before launching agents, confirm:

1. **Subsystem name** — what the user is auditing (e.g., "the BD module," "the pipeline subsystem").
2. **Code paths** — concrete file/folder paths that belong to this subsystem. If the project has a registered-subsystems table (some projects maintain one in CLAUDE.md or a dedicated file), use it. If not, ask the user to confirm paths rather than guessing.
3. **Related spec(s)** — is there a spec file for this subsystem? Path.
4. **Related procedure(s) or tests** — any procedure docs or test directories that correspond to this subsystem?

If you can't get concrete paths, stop and ask. Do not guess paths — auditing the wrong code produces a report the user cannot act on.

### Project-specific registered subsystems

Some projects maintain a registered-subsystems table, mapping subsystem names to code paths and related procedures/specs. Example from a mature project:

```
| Name     | Code paths                           | Procedure/spec                    |
|----------|--------------------------------------|-----------------------------------|
| BD       | aims/web/bd/, aims/web/routes/bd/    | aims/procedures/bd-hygiene.md     |
| Projects | aims/web/routes/projects.py          | aims/procedures/project-lifecycle |
| ...      | ...                                  | ...                               |
```

If a project has this, type `/analyze BD` resolves the paths from the table. If the project does not have this table yet, consider suggesting one — it makes audit invocation a single word instead of a path-lookup conversation.

## Step 2 — Launch four parallel subagents

Launch all four in a **single message with multiple tool calls.** Each agent is briefed as a self-contained task.

### Agent A — Code hygiene (dead code, duplication, consistency, inline docs)

> Audit the `{subsystem-name}` subsystem at paths: `{paths}`. Find:
> - Unused functions, classes, routes, templates, or imports (cross-check with grep).
> - Duplicated logic across files that could be unified.
> - Overly complex functions (>80 lines, deep nesting) that would benefit from a rewrite.
> - Naming / style inconsistencies with the rest of the codebase.
> - Missing or misleading docstrings on public functions.
> - Dependencies that are no longer used.
>
> Report a punch list with severity (CRITICAL / MODERATE / MINOR) and `file:line` references. Under 250 words. Do not fix anything.

### Agent B — Test coverage and test plan assessment

> Check test coverage for the `{subsystem-name}` subsystem at paths: `{paths}`. Look in `{test directories, from step 1}` and read `tests/TEST_PLAN.md`. Answer:
> - Which routes / functions have direct test coverage?
> - Which have NO test coverage but are user-facing or load-bearing?
> - Are there obvious happy-path flows untested?
> - Are there skipped (`@skip`) or flaky tests?
> - What does `tests/TEST_PLAN.md` say about this subsystem? Is it listed? Are the listed checks still accurate?
> - Are there tests that capture **current actual behavior** (characterization tests), or do tests only cover intended behavior?
> - Recommend: should tests be added? If so, which scenarios are highest value — prioritize code that handles money, security, data integrity, or is about to be changed.
>
> Report a punch list: what's tested, what's not, what the test plan says vs reality, with severity. Under 300 words. Do not write tests.

### Agent C — Spec drift

> Compare the actual code at paths `{paths}` against the spec canvases and spec files at `{spec/procedure files, if any}`. For each concrete claim the spec makes (paths, table/field names, endpoint signatures, behavioral claims, counts, configuration), verify it against the code.
>
> Categorize each finding:
> - **Spec is wrong (code moved)** — code evolved, spec wasn't updated
> - **Spec is wrong (renamed/relocated)** — feature exists but at a different path/name. Before concluding "deleted," search for similar patterns — prefix drops, namespace reorgs, promotions to root paths
> - **Code is wrong (spec was intent)** — code drifted from intended behavior (a bug)
> - **Both wrong / ambiguous** — neither matches stated intent
> - **Stale metric** — hardcoded counts that drift naturally
> - **Undocumented** — behavior in code with no spec coverage (silent omissions — the most dangerous kind of drift)
>
> Report a punch list with `file:line` on both sides (spec and code). Under 300 words. Do not fix anything.
>
> _If no spec exists for this subsystem, note: "No spec exists — flag as CRITICAL gap."_

### Agent D — Operations & security (external calls, cron, permissions, observability)

> Audit the `{subsystem-name}` subsystem at paths: `{paths}` for operational and security concerns. Find:
> - Destructive actions (delete, modify, send email, write to shared data) without permission checks.
> - User inputs not validated at entry points.
> - Missing or inconsistent audit logging on sensitive actions.
> - Scheduled operations (cron, systemd timers, background tasks) that are not documented.
> - External API calls without timeouts or failure handling.
> - Secrets hardcoded where they should be environment variables.
> - Operations that fail silently (caught exception → no log, no alert).
>
> Report a punch list with severity and `file:line` references. Under 250 words. Do not fix anything.

## Step 3 — Merge into a single punch list

After all four agents return, merge and dedupe. Format:

```markdown
# Audit — {subsystem-name}

**Date:** {YYYY-MM-DD}
**Subsystem:** {name}
**Paths audited:** {paths}
**Health grade:** Green / Yellow / Red

---

## Summary

Two to three sentences. What's the overall health? Biggest risk? One thing to do first.

---

## CRITICAL (fix soon)

- [hygiene] {one-line finding} — `file:line`
- [coverage] {one-line finding} — `file:line`
- [drift] {one-line finding} — `file:line`
- [ops] {one-line finding} — `file:line`

## MODERATE

- [hygiene] ... — `file:line`
- ...

## MINOR

- ...

---

## Recommended first action

If the user only has 30 minutes, do this one thing: {specific item from above, with reason}.

---

## Spec drift findings

### Spec is wrong (code moved)
- [drift] {claim in spec} → {reality in code} — `spec-file:line` vs `code-file:line`

### Code is wrong (spec was intent)
- [drift] {claim} → {reality} — needs Hodos run to fix code

### Undocumented (silent omissions)
- [drift] {feature/behavior in code with no spec coverage}

### Confirmed accurate
- {spec claim verified} — ✅

---

## Test plan recommendations

### Tests that should be added
- {scenario description — what the code currently does, not desired behavior} — `file:line`

### Existing tests that are stale
- {test that passes but tests changed behavior} — `test-file:line`

### Test plan entries to update
- {entry in TEST_PLAN.md that needs correction}

---

## Notebook entry

Link: `notebook/{YYYY-MM-DD}-analyze-{subsystem}.md`

Record findings that aren't documented elsewhere — patterns noticed, architectural concerns,
context that would be lost if not written down. This is the persistent record of what was
found during the analysis.

---

## Recommended next actions

For each recommendation, name the skill and what to hand off:

1. {Action} — via `/write-spec` (targeted edit) with findings {D-1, D-2}
2. {Action} — update `tests/TEST_PLAN.md` with scenarios {list}
3. {Action} — route through main workflow (brainstorm → plan → execute) for code-wrong findings

---

## Gaps flagged (meta-findings about the audit itself)

- No spec exists for this subsystem
- No procedure file at expected path
- Registered-subsystems table does not include this subsystem
- (etc.)

---

## Out of scope for this audit

- Integration tests against production data
- Load / performance testing beyond static analysis
- Manual UI / accessibility review
- (Whatever wasn't checked — be explicit so the reader knows what still needs doing)
```

## Step 4 — Test plan recommendations

After merging the audit report, review Agent B's test coverage findings against `tests/TEST_PLAN.md` and produce concrete recommendations:

1. **Tests that should be added** — highest-value gaps: user-facing paths with no coverage, code handling money/security/data, code about to be changed. For each, describe the scenario to test (what it does now, not what it should do).
2. **Existing tests that are stale or wrong** — tests that pass but test behavior that has changed, or tests that are skipped/flaky.
3. **Test plan entries that need updating** — entries in `tests/TEST_PLAN.md` that reference this subsystem but are inaccurate.

Include these in the report under a **Test Plan Recommendations** section. Do not write the actual tests — recommend what should be tested and let the user decide whether to proceed.

If the user approves, chain into updating `tests/TEST_PLAN.md` with the recommended additions.

## Step 5 — Update notebook

Create or update a notebook entry at `notebook/{YYYY-MM-DD}-analyze-{subsystem}.md`. Record findings that aren't captured elsewhere — patterns noticed, architectural concerns, surprising discoveries, context that would be lost if not written down. Link to the audit report in `plans/`.

## Step 6 — Recommend next actions with concrete handoffs

After presenting the report, recommend specific next actions. Each recommendation must name the skill and what to pass to it — not just "fix the spec."

**Spec drift (spec is wrong):**
- Recommend: `/write-spec` in targeted edit mode
- Hand off: the specific drift findings (which claims are wrong, what the code actually does, `spec-file:line` and `code-file:line`)
- Write-spec uses that to propose diffs the user can approve

**Spec drift (undocumented / silent omissions):**
- Recommend: `/write-spec` in bootstrap or full create mode
- Hand off: what the code does that has no spec coverage, which canvas type is needed (database CSV, interaction matrix, etc.)

**Test gaps:**
- Recommend: update `tests/TEST_PLAN.md` with the specific scenarios identified by Agent B
- Hand off: the scenarios to add, which are highest priority, what existing entries need updating

**Code-wrong findings (spec was intent, code drifted):**
- Recommend: route through the main workflow — brainstorm → write-plan → execute-plan
- Hand off: the specific findings showing what the spec says vs what the code does
- These are bugs, not maintenance — they need the full discipline

**Both-wrong / ambiguous:**
- Recommend: escalate to user for a decision before any action
- Present the disagreement clearly: what spec says, what code does, why neither is obviously right

The analysis report is saved at `plans/` regardless of whether the user acts — it's a decision log.

## Rules

1. **Always four parallel agents, in a single message.** Sequential auditing drops quality. Parallel is not optional.
2. **Agents must be briefed concretely.** Pass the actual paths — not a placeholder. Each agent's prompt must be self-contained because they don't share context.
3. **Final report should be concise.** Tighten findings. Drop the weakest MINOR items. The report covers more ground now (code, specs, tests, ops) — keep each section punchy.
4. **Dedupe overlapping findings.** The same issue surfaced by multiple agents is *stronger signal*, not cause for three line items. Mention it once, note that multiple angles found it.
5. **Tag each finding with its category** (`[hygiene]`, `[coverage]`, `[drift]`, `[ops]`). So the reader can see the distribution at a glance.
6. **Green / Yellow / Red grade is required.** Top of the report. No "Inconclusive" cop-out.
   - Green: production-ready, minor debt only
   - Yellow: functional, significant debt
   - Red: urgent issues (failing tests, security gaps, broken assumptions)
7. **Recommend a first action.** The "if you only have 30 minutes" item is part of the output, not optional.
8. **Include an "out of scope" section.** The reader must know what the audit did NOT check.
9. **Read-only.** Never modify code during an audit.

## What you do NOT do in this skill

- Do not run audits sequentially — parallel agents or nothing
- Do not modify any code
- Do not audit more than one subsystem per run
- Do not skip the "out of scope" section
- Do not grade "Inconclusive" — every audit ends Green/Yellow/Red
- Do not invent findings when the code is clean — Green is a valid grade
- Do not fix issues, even ones that look trivial — this skill reports only
