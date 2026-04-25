---
description: Targeted edit of an existing spec file — preserve structure and voice, propose diffs before applying, redirect to /write-spec for full rewrites
---

# /update-spec

## When to use

Invoke when the user says any of:
- "Update the spec"
- "Fix the spec for X"
- "The spec is wrong about Y"
- "I just changed X, update the spec to match"
- "Update [spec-file-name].md"

Two common modes:

1. **User-driven:** the user knows what changed and tells you. "I added a verified-customer tag to companies — update the Companies page spec."
2. **Investigation-driven:** the user suspects the spec is stale but doesn't know what. "The BD features spec hasn't been touched in months — update it to match current reality."

Do **not** use this skill if:
- The spec doesn't exist yet — that's `/write-spec` (create).
- More than ~30% of the spec needs changing — that's a rewrite, not an update. Redirect to `/write-spec`. Flag to the user and stop.
- You just want to check where specs are stale in general — that's `/spec-drift-check` (scan).

## Core discipline — targeted edits only

This skill is a scalpel, not a rewrite tool. Rules:

1. **Preserve structure.** Keep the spec's existing section order, heading style, and formatting conventions. If the spec uses tables, edits stay in tables. If prose, stay in prose.
2. **Preserve voice.** If the existing spec is terse, your edits stay terse. If it's verbose, match that. Don't swap in Claude's default voice.
3. **Change only what's stale.** Accurate sections stay untouched.
4. **Propose before applying.** Show the user the diff. Let them approve, reject, or redirect each edit.
5. **Flag disagreements.** If the spec and code genuinely disagree and neither is obviously right, that's a user decision — not something this skill resolves.

## Process

### Step 1 — Identify the spec

Confirm which spec file. If the user named it, use that. If ambiguous ("update the database spec" could be `spec/03-database.md` or a newer file), ask.

### Step 2 — Read the spec fully

Understand the existing structure before touching anything. Note:
- What sections exist
- What style conventions are used (tables vs prose, heading depth, link format)
- What voice is used (terse, verbose, formal, informal)

### Step 3 — Gather what changed

Depends on mode:

**User-driven:**
- User tells you what changed (new field, new page, new behavior, renamed function, etc.)
- Optionally, read the referenced code to confirm the change
- Skip scanning — the user knows the scope

**Investigation-driven:**
- Read the code that backs the spec's claims
- Compare spec claims against code reality
- Build a list of stale items (similar to `/spec-drift-check`, but producing edits rather than a report)
- **Actively scan for rot patterns.** As you read, flag any hardcoded counts, exhaustive route lists, full column enumerations, or "currently N..." statements. These always drift. Propose replacing each with a pointer to the source file (see rule #5 below).

### Step 4 — Decide: update or redirect?

Count the distinct sections that need changes:

- **<30% of the spec stale:** proceed with `/update-spec` — targeted edits make sense.
- **≥30% of the spec stale:** stop. The spec has drifted too far for surgical fixes. Report to the user and recommend `/write-spec` for a clean rewrite.

This threshold exists because scalpel work on a mostly-rotten spec produces a patchwork that's harder to read than a fresh rewrite. Better to acknowledge the situation.

### Step 5 — Propose the diffs

For each section needing an edit, produce a before/after showing the specific change. Group the diffs by severity.

```markdown
# Update Spec — {spec-file-name}

**Date:** {YYYY-MM-DD}
**Spec:** {link to spec file}
**Mode:** User-driven / Investigation-driven
**Scope estimate:** {N sections need edits, ~X% of spec}

---

## Proposed edits

### E-1: {short description}
**Section:** {heading}
**Why it's stale:** {user said / code shows}

**Before:**
```
{exact current text}
```

**After:**
```
{proposed replacement}
```

### E-2: ...

---

## Disagreements needing user decision

### D-1: {short description}
**Spec says:** "{quote}"
**Code does:** {behavior with file:line}
**Cannot auto-resolve because:** {the spec may have been the intent, or the code may have evolved for good reason}
**User decision:**
- [ ] Keep spec, change code to match (→ Hodos run)
- [ ] Update spec to match code (→ apply E-X as above)
- [ ] Both are wrong; need deeper design decision

---

## Sections verified accurate (no edits proposed)

- {heading} — confirmed against {file}
- {heading} — confirmed

---

## Recommendation

- **If user approves all edits:** apply in one commit with message "Update {spec-file}: {short summary}."
- **If user rejects some:** re-propose the approved subset, discard the rest.
- **If edits reveal a disagreement (D-N above):** resolve with user before applying related edits.
```

### Step 6 — Apply approved edits

After user approval:
- Apply edits exactly as shown in the diff. No last-minute "while I'm in there" cleanups.
- Update the "Last updated" or version field in the spec if one exists.
- Commit with a descriptive message naming the spec and the nature of the change.

## Rules

1. **Never edit without proposing.** The user sees every change before it lands. Exception: if the user explicitly says "just do it, I trust you," the risk is theirs.
2. **One spec per run.** Don't batch updates to multiple spec files in a single skill invocation. Each spec gets its own update run.
3. **Structure preservation is load-bearing.** If you want to reorganize the spec (merge sections, reorder, add new structure), that's a rewrite, not an update — redirect to `/write-spec`.
4. **Don't resolve disagreements.** If the spec and code disagree, report the disagreement. Let the user choose which side wins.
5. **Trim rot — replace hardcoded values with pointers to source.** Certain kinds of content drift naturally and should never be hardcoded in a spec:
   - **Row counts / data volumes:** "3,906 companies," "currently 60 projects" — these are stale the moment they're written. Remove the number, or convert to "approximate as of {date}" if the scale is load-bearing.
   - **Exhaustive lists that live in code:** full lists of routes, tables, columns, field names, feature flags, cron jobs, dependencies. These belong in the source file, not mirrored in a spec where they will rot.
   - **Pattern for replacement:** remove the hardcoded content and add a pointer to the source of truth. Example:
     - Before: "Routes: /companies, /contacts, /leads, /projects, /pipeline, /campaigns, /contact-tiering, ..." (12 routes listed)
     - After: "Routes: see `aims/web/bd/routes/` for the current list. Route file is the source of truth; this spec describes structure and purpose, not enumeration."
   - This rule matters because a spec that enumerates things will always drift. A spec that points to the source of truth cannot drift by nature — it just describes *categories* and *patterns*, leaving *enumeration* to the code.

6. **Accurate sections stay put.** The spec's existing correct content is valuable. Don't rewrite it for style.

7. **Architectural claims require verification.** Any edit that makes or modifies a sweeping architectural statement — "single database," "three services," "PostgreSQL-only," "monolithic Flask app," "synchronous pipeline" — must be verified by reading at least one file in the relevant code path before being proposed. Sweeping claims are where hallucinations hide; they feel concrete in prose but have no textual anchor in the spec itself. Verification examples:
   - Editing a "Database" section? Read `db.py` or equivalent; check what driver is imported (`sqlite3` vs `psycopg2`), what connection string format is used, how many database files/connections exist.
   - Editing an "Architecture" section? Read the main entrypoint, the route registration, the service layer — confirm the described structure actually matches.
   - Editing a "Services" or "Integrations" section? Check the actual service directory or integration files; count them; note what's there vs what's claimed.

   If you can't verify a claim from code, say so in the proposal rather than asserting it. "Based on existing spec text (unverified)" is honest; a confidently wrong architectural claim propagates into every downstream reader of the spec.

## Difference from sibling skills

| Skill | Purpose | Output |
|---|---|---|
| `/write-spec` | **Create** a feature/event spec (or full rewrite after massive drift) | New or completely rewritten spec file |
| `/update-spec` | **Targeted edit** of an existing spec | Diff proposals + applied edits, preserving structure |
| `/spec-drift-check` | **Scan** for spec/code divergence across a spec | Report only, no edits |

`/update-spec` is the edit verb. `/write-spec` is the create verb. `/spec-drift-check` is the assess verb.

## Output handoff

- Diff proposal file written to `.claude/plans/{YYYY-MM-DD}-update-{spec-name}.md`
- User reviews, approves or rejects each edit
- Approved edits applied to the spec file
- Plan file kept as decision log (why each edit was made)
- Commit message references both the spec and the plan file

## What you do NOT do in this skill

- Do not rewrite sections that are already accurate
- Do not apply edits before the user has seen the diff (unless explicitly authorized)
- Do not change the spec's structure or voice
- Do not resolve spec-vs-code disagreements unilaterally — report them and wait
- Do not update more than one spec file in a single run
- Do not proceed if >30% of the spec needs editing — redirect to full rewrite
