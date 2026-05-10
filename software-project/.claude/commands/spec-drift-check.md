---
description: Scan where code and spec have diverged. Report findings — do not silently fix either side
---

# /spec-drift-check

## When to use

Invoke when:
- A spec file has not been updated in a while and you suspect code has drifted
- Before running `/write-spec` — to know what's actually out of sync
- As part of a periodic health check (monthly, quarterly)
- Before a significant change to a subsystem — confirm you're working from accurate assumptions
- After a long vibe-coding session — verify the spec still describes reality

Do **not** use to auto-fix drift. That's `/write-spec` (if the spec is wrong) or a Hodos run (if the code is wrong). `/spec-drift-check` reports only.

## What you must produce

A drift report at `plans/{YYYY-MM-DD}-drift-{spec-name}.md`.

## Process

### Step 1 — Read the spec fully

Read every section. Note every concrete claim the spec makes:
- **Paths** — "the schema is at X/Y/Z"
- **Table/field names** — "the companies table has a `verified` column"
- **Function/endpoint signatures** — "POST /api/verify takes a company_id"
- **Behavioral claims** — "when X happens, Y is updated"
- **Counts / metrics** — "there are 14 tables" (these are usually already stale)
- **Configuration** — "the default timeout is 30 seconds"

### Step 2 — Verify each claim against code

For each claim, read the actual code. Categorize:

| Verdict | Meaning |
|---|---|
| ✅ Spec matches code | Current and accurate |
| ❌ Spec is wrong (code moved) | Code evolved, spec wasn't updated — spec drift |
| ❌ Spec is wrong (renamed/relocated) | The feature exists, but at a different path / under a different name — see "renamed-route check" below |
| ❌ Code is wrong (spec was the intent) | Code drifted from intended behavior — a bug |
| ⚠️ Both are wrong / ambiguous | Spec and code disagree, and neither matches the stated intent |
| 🟡 Stale metric / count | Numbers that were accurate when written but drift naturally (row counts, LOC, etc.) |

**Renamed-route check — required before concluding "deleted."**

When a route, path, file, or function claimed in the spec is missing from code, do NOT immediately conclude "feature deleted." First check whether a similarly-named or same-purpose alternative exists elsewhere:

- **Route missing?** Search for similar patterns (`/bd/call-screen` missing → is there `/contact/<id>/call`? `/bd/companies` missing → is there `/companies`? `/bd/admin/approvals` missing → is there `/admin/bd-contact-approvals`?). Common rename patterns: prefix drops, namespace reorganization, promotion to root paths.
- **File missing?** `grep` for the expected filename or key function names at different paths — e.g., `BD/services/ai_writer.py` missing → is there `aims/web/bd/services/llm/router.py` doing the same job?
- **Function missing?** Search for the function by purpose, not just name. A `classify_reply_sentiment()` may have become `classify_reply()`.

If a match is found with the same purpose, classify as "Spec is wrong (renamed/relocated)" — the fix is updating the spec to point to the new location, not removing the claim entirely. This matters because deleting a claim for a feature that still exists creates new drift in the opposite direction.

**Do not fix anything in this skill.** You are reporting. The user decides which side to correct.

### Step 3 — Look for silent omissions

What is present in the code but *not mentioned in the spec at all*? New features, new tables, new routes, new integrations. These are the most dangerous kind of drift — the spec doesn't lie, it just doesn't know.

Scan the code for:
- New files in directories the spec references
- New functions in files the spec describes
- New routes, new commands, new skills
- New configuration variables
- New external integrations

### Step 4 — Report

## File template

```markdown
# Spec Drift Check — {spec file name}

**Date:** {YYYY-MM-DD}
**Spec reviewed:** {link to spec file}
**Spec last-modified:** {date from file history}
**Reviewer:** Claude (via /spec-drift-check)

---

## Summary

One paragraph. Is the spec fundamentally accurate, partially drifted, or badly out of date? How much work to reconcile?

**Drift severity:** Minor / Moderate / Major

---

## Findings

### ❌ Spec is wrong (code has moved on)

#### D-1: {short name}
- **Claim in spec:** "{exact quote or paraphrase, with section reference}"
- **Reality in code:** {what the code actually says, with file:line}
- **Suggested action:** Update spec via `/write-spec` — the code is the current truth.

#### D-2: ...

---

### ❌ Code is wrong (spec was intent)

#### D-3: {short name}
- **Claim in spec:** "{quote}"
- **Reality in code:** {what's different}
- **Suggested action:** Either fix code via Hodos pipeline, or confirm with user that the intent has changed and update spec.

---

### ⚠️ Both wrong or ambiguous

#### D-4: {short name}
- **Spec says:** "{quote}"
- **Code says:** {behavior}
- **Neither matches:** {what the user has told you or what the broader context suggests}
- **Suggested action:** User decision required. Cannot auto-resolve.

---

### 🟡 Stale metrics (natural drift, low priority)

- Spec says "14 tables" — code has 30. Remove the count, or mark it as "approximate, as of {date}."
- Spec says "processed ~500 leads per week" — no way to verify without data. Remove or flag.

---

### Undocumented (silent omissions)

Things present in code with no spec coverage:

- **{feature/module/file}** — {one-line description}
- **{feature}** — ...

Suggested action: write new spec sections via `/write-spec` as appropriate.

---

### ✅ Confirmed accurate

List of spec claims explicitly verified and current. This matters — it tells the user what parts of the spec they can still trust.

- Section "X" claims Y — confirmed.
- ...

---

## Recommended next steps

1. {Most important action — usually fix the spec first, since it's cheap}
2. {Next}
3. {Defer}

---

## Open questions for user

- [ ] Which side should win on D-3? (Spec says X, code does Y — was X the intent, or has it changed?)
- [ ] Is the {undocumented feature} intentional and worth documenting, or technical debt to remove?

---

## Offer: apply spec-wrong fixes via `/write-spec`

For the findings where the **spec is wrong and the code is the current truth** (the "Spec is wrong" section above), `/write-spec` can turn these findings into concrete edit proposals. Would you like to chain into `/write-spec` now to fix them?

- Yes → invoke `/write-spec {spec-name}.md` with this drift report as input. It will produce diff proposals for each spec-wrong finding for your approval.
- No → the drift report stays as a standalone document for later reference.

(Code-wrong and both-wrong findings are NOT candidates for `/write-spec` — those need a Hodos run or a user decision, respectively.)
```

## Rules

1. **Report, don't repair.** This skill does not edit code or spec files. That's `/write-spec` or Hodos.
2. **Be specific.** "The spec is out of date" is not a finding. "Spec section 3.2 says the BD schema lives at `BD/schema.sql`; actual location is `aims/web/bd/schema/migrations/`" is a finding.
3. **Sort by severity.** Code-wrong findings are bugs. Spec-wrong findings are documentation work. Both wrong = user judgment required.
4. **Note what's accurate.** An all-findings-and-no-confirmations report reads like the spec is 100% broken. Report the ✅ items too.
5. **Silent omissions matter most.** If code has a whole new subsystem the spec doesn't mention, that's worse than a wrong path string.
6. **One file at a time.** Don't try to drift-check an entire spec folder in one pass. Pick one spec file, do it properly.

## Output handoff

After the drift report is produced:

1. Present the report to the user.
2. **Offer to chain to `/write-spec`** for the "Spec is wrong" findings — these are the cheap wins. If the user approves, invoke `/write-spec` with the drift report as input; it will produce diff proposals for each.
3. "Code is wrong" findings are flagged for a separate Hodos run (they require actual code changes).
4. "Both wrong or ambiguous" findings stay open until the user makes a decision.
5. The drift report is saved at `plans/` regardless of whether the user chooses to act on it — it's a decision log.

## What you do NOT do in this skill

- Do not edit the spec (but you can offer to chain into `/write-spec` which does)
- Do not edit the code
- Do not invent findings to look thorough — confirmed-accurate is a valid outcome
- Do not skip the silent-omissions scan (it's where the real drift hides)
- Do not auto-run `/write-spec` — always offer and wait for user approval before chaining
