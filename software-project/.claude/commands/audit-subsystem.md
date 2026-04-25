---
description: Health check on one module or subsystem using 4 parallel Explore subagents — code hygiene, tests, spec drift, ops/security. Produces a concise punch list, not a wall of text
---

# /audit-subsystem

## When to use

Invoke when:
- You haven't touched a subsystem in a while and want to know its health
- Before a significant change to a subsystem — know what debt you're inheriting
- Periodically (monthly or quarterly) as a maintenance practice
- Before adding new features to an older subsystem
- After a project takeover or onboarding

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

If a project has this, type `/audit-subsystem BD` resolves the paths from the table. If the project does not have this table yet, consider suggesting one — it makes audit invocation a single word instead of a path-lookup conversation.

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

### Agent B — Test coverage

> Check test coverage for the `{subsystem-name}` subsystem at paths: `{paths}`. Look in `{test directories, from step 1}`. Answer:
> - Which routes / functions have direct test coverage?
> - Which have NO test coverage but are user-facing or load-bearing?
> - Are there obvious happy-path flows untested?
> - Are there skipped (`@skip`) or flaky tests?
>
> Report a punch list: what's tested, what's not, with severity. Under 250 words. Do not write tests.

### Agent C — Spec / procedure drift

> Compare the actual code at paths `{paths}` against the spec or procedure at `{spec/procedure files, if any}`. Find:
> - Claims in the spec that no longer match the code (renamed routes, removed features, changed data models).
> - Behavior in the code that the spec does not describe (new features added without spec update).
> - Hardcoded counts, exhaustive enumerations, or "currently N..." claims that will naturally drift.
>
> Report a punch list with `file:line` on both sides. Under 250 words. Do not fix anything.
>
> _If no spec exists for this subsystem, skip this agent and note in the final report: "No spec exists for this subsystem — flag as CRITICAL gap."_

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

## Rules

1. **Always four parallel agents, in a single message.** Sequential auditing drops quality. Parallel is not optional.
2. **Agents must be briefed concretely.** Pass the actual paths — not a placeholder. Each agent's prompt must be self-contained because they don't share context.
3. **Final report under 500 words.** If findings exceed that, tighten. Drop the weakest MINOR items.
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
