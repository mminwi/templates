# Plan: Tighten Hodos Specification Convergence

## Status

Superseded by `2026-05-10-software-project-specification-convergence-v3.md`. This version is retained only as a historical intermediate artifact.

## Purpose

Capture the current working direction for modifying `software-project/` so Hodos has a stronger specification-convergence layer between loose human brain dumps and implementation code.

This is not implementation yet. It is the saved artifact for the concept so the work does not depend on chat context.

## Context

The `software-project/` template already provides a strong Hodos workflow for AI-assisted software work. It emphasizes:

- Brainstorming
- Planning
- Critic review
- Execution
- Verification
- Spec drift checks
- Skeleton/framework-first implementation
- Separation of `specs/` and `vision/`

The weak point appears to be the specification step.

The issue is not only that specs need to be “better written.” The deeper issue is that loose narrative intent is being converted into plans too quickly. Planning is being asked to perform too many jobs at once:

- Extract requirements
- Resolve ambiguity
- Detect contradiction
- Define behavior
- Decompose work
- Sequence implementation
- Set acceptance criteria
- Bound agent scope

That makes the plan either too broad or too vague. When execution begins, the coding agent still has to make product/design decisions. On small programs this works. On larger programs it causes drift.

## Core Problem

Current rough flow:

```text
brainseed / brainstorm
  → plan
  → critic
  → execute
```

This skips a strong convergence layer between loose human narrative and implementation planning.

The next evolution of `software-project/` should make that missing layer explicit.

## Core Direction

The `software-project/` template should add a specification-convergence layer that sits between brain dumps and code plans.

Proposed Big-tier / muddy Medium-tier flow:

```text
brainseed
  → extract-spec
  → normalize-spec
  → resolve-spec
  → slice-work
  → plan-one-slice
  → critic
  → execute
  → verify
```

Small work should remain lightweight. This added structure is for work where loose narrative would otherwise force the execution agent to make product decisions.

## Principle: Chat Is Working Memory, Files Are Project Memory

Plans, requirements, decisions, and spec rules are not durable if they only live in chat.

For Hodos:

- A plan is not real until it exists as a saved artifact.
- Chat-only plans are reasoning, not project state.
- Anything that must survive context loss must be written to a project artifact.
- Ask/brainstorming is useful for exploration, but code/spec work must eventually anchor in files.

## Specification Convergence Flow

### Brainseed

Purpose: capture messy human intent freely.

Brainseed can include long voice-dictated narrative, uncertainty, examples, contradictions, and reasoning. It is raw material, not contract.

Rule:

```text
Brainseed is context, not authority.
```

### Extract Spec

Purpose: convert narrative into candidate structured objects.

Candidate objects may include:

- Needs
- Requirements
- States
- Modes
- Commands
- Guard conditions
- Validation rules
- Failure modes
- Workflows
- Invariants
- Open questions
- Contradictions
- Deferred ideas

Rule:

```text
Extract and label. Do not silently resolve.
```

### Normalize Spec

Purpose: clean up extracted objects before the user reviews them.

Normalize should:

- Split multi-part requirements
- Merge duplicates
- Separate vision from spec
- Separate now from later
- Identify vague wording
- Identify hidden assumptions
- Mark contradictions
- Convert broad statements into atomic candidate requirements
- Normalize terminology toward software terms while preserving meaning

Rule:

```text
Terminology can converge. Meaning must not drift.
```

### Resolve Spec

Purpose: close ambiguity before planning.

The AI should ask targeted questions rather than writing more prose.

Outputs:

- Accepted requirements
- Deferred requirements
- Rejected requirements
- Decisions
- Remaining blockers
- Updated open questions

Rule:

```text
A plan should not be written until blocking ambiguity is resolved or explicitly deferred.
```

### Slice Work

Purpose: break accepted specification into agent-sized implementation packets.

A slice should be small enough that one fresh coding agent can complete it with:

- The slice description
- Linked spec objects
- Relevant local code context
- Acceptance criteria
- Explicit exclusions

Distinction:

```text
Spec says what must be true.
Slice says what one agent should change now.
Plan says how that slice will be changed.
```

## Proposed Spec Table Layer

The most important new artifact family is not prose. It is structured, spreadsheet-like CSV tables that both the user and AI can inspect.

The user reviews tables better than JSON. The AI can translate tables into JSON, Markdown, and implementation plans. CSV is preferred as the first implementation because it is:

- Fast for the AI to generate
- Plain text
- Repo-native
- Easier to convert to JSON/Markdown
- Reviewable in a table/grid viewer
- Less brittle than Markdown tables for human editing

Excel may remain useful later, but first-pass Hodos should use CSV as the shared human/AI authoring surface.

## Spec CSV Rules

Hodos spec CSVs are structured specification artifacts.

Rules:

- Keep one header row.
- Keep every row at the same column count.
- Do not leave blank cells.
- Use explicit values such as `none`, `n/a`, `unknown`, or `tbd`.
- Do not rely on color, formatting, filters, or formulas for spec meaning.
- Color/formatting may be used only as human visual cues.
- Do not use formulas in authoritative spec CSVs.
- Do not use merged cells.
- Avoid multi-line cells.
- Add or remove columns only when explicitly working on the table structure.

Meaning-preservation rule:

- The AI may clarify wording, normalize names, and align terminology with the codebase.
- The AI must preserve the underlying meaning of each row and column unless explicitly asked to change it.
- Converting an unresolved question into a decision requires explicit approval.

## Database Field Spec

Add a first-class database field table for project-wide stored data.

Proposed path:

```text
software-project/specs/database/database-fields.csv
```

Purpose:

```text
Define what stored data exists in the system.
```

Possible columns:

```text
entity,field,data_type,required,default_value,primary_key,unique,foreign_key,validation,notes
```

This table answers:

- What tables/entities exist?
- What fields/columns exist?
- What type is each field?
- Is it required?
- What is its default?
- Is it a key or relationship?
- What validation applies?

## Page Interaction Matrix

Add page-specific interaction matrices.

Preferred term:

```text
interaction matrix
```

Purpose:

```text
For one page/interface, define what inputs, stored data, calculated values, outputs, states, and actions exist.
```

This is not the global database schema. It is per page and should include only what that page reads, displays, edits, calculates, or writes.

Proposed path pattern:

```text
software-project/specs/pages/<page-name>/interaction-matrix.csv
```

Example:

```text
software-project/specs/pages/estimating/interaction-matrix.csv
software-project/specs/pages/project-planning/interaction-matrix.csv
```

### Interaction Matrix Row Order

Row order matters for review.

Preferred order:

```text
1. Inputs
2. Stored data
3. Calculated / derived values
4. Outputs
```

Reason:

- Inputs are what the user gives the page.
- Stored data is what the page reads from or writes to.
- Calculated values are derived from inputs and/or stored data.
- Outputs are what the page displays, emits, or sends onward.

The page may write results back into stored data, but the review flow still works best when rows are grouped in this top-to-bottom order.

### Interaction Matrix Columns

Rows are page items. Columns describe identity, state behavior, actions, validation, and notes.

Possible columns:

```text
section,item_id,label,item_type,data_source,destination,state_draft,state_submitted,state_approved,on_load,on_change,on_save,on_submit,validation,notes
```

The exact columns should be refined later.

Working vocabulary:

- Input field
- Stored data field
- Calculated field
- Output field
- Action
- Lifecycle state
- Read-only
- Locked
- Editable
- Hidden
- Disabled
- Required
- Optional
- Validation
- Guard condition
- Invariant

State behavior should usually be represented as columns such as:

```text
state_draft,state_submitted,state_approved,state_archived
```

Actions should usually be represented as columns such as:

```text
on_load,on_change,on_save,on_submit,on_approve,on_archive
```

## Directory Structure Candidate

This is a candidate directory structure, not yet final:

```text
software-project/
  specs/
    database/
      README.md
      database-fields.csv
    pages/
      README.md
      <page-name>/
        interaction-matrix.csv
    generated/
      README.md
      database-schema.json
      page-models.json
      schema-report.md
```

Generated files should eventually be clearly marked. The source-of-authoring should be the CSV tables unless a later decision changes this.

## Authority Model Candidate

Candidate rule:

```text
CSV tables are the human/AI authoring surface.
Generated JSON is the normalized AI/code contract.
Generated Markdown is the review/report surface.
```

If generated JSON or Markdown disagrees with the CSV source table, regenerate the generated artifact.

If code disagrees with accepted specs, stop and ask whether the spec or code should change.

## Critic Questions To Add Later

The critic should not only ask whether a plan is plausible. It should ask:

- Is the spec tight enough that multiple fresh agents would implement the same behavior?
- Are any table cells `unknown` or `tbd` in a way that blocks implementation?
- Is the interaction matrix page-specific, or did it accidentally pull in unrelated system scope?
- Are row meanings preserved after terminology cleanup?
- Are actions and states represented explicitly enough?
- Is the slice small enough for one coding agent?
- Does the plan require the executor to make product decisions?

## Open Questions

These should be resolved before implementation:

- Should `database-fields.csv` ship as an empty template, a documented example, or both?
- Should each page get a folder with `interaction-matrix.csv`, or should all page matrices live in one folder with page-prefixed filenames?
- What exact columns belong in the first version of `interaction-matrix.csv`?
- Should state columns be fixed by page, or should states be listed in a separate `states.csv`?
- Should action columns be fixed by page, or should actions be listed in a separate `actions.csv`?
- How should generated JSON be produced: script now, or documented future step?
- Should the template include sample CSV files with fake data, blank CSV templates, or both?
- Where should CSV table rules live: `specs/README.md`, new `specs/tables/README.md`, or `hodos.md`?
- Should new skills be added now, or should skills wait until the table format stabilizes?

## Skill Changes Deferred

Skill work is the next major topic, but this plan does not implement it yet.

Likely future skills:

- `extract-spec`
- `resolve-spec`
- `slice-work`
- table generation/update helpers
- table consistency checks

For now, this plan only captures the concept and candidate template changes.

## Implementation Plan Candidate

### Step 1 — Add spec-table documentation

Update the template documentation to explain:

- CSV spec tables
- No-blank-cell discipline
- Meaning preservation
- CSV as human/AI authoring surface
- Generated JSON/Markdown as later views

Likely files:

```text
software-project/specs/README.md
software-project/hodos.md
```

### Step 2 — Add database spec seed

Create a `specs/database/` folder with:

```text
README.md
database-fields.csv
```

The CSV should probably be a blank template or minimal example.

### Step 3 — Add page interaction matrix seed

Create a `specs/pages/` folder with:

```text
README.md
_template/interaction-matrix.csv
```

The template should preserve the preferred row grouping:

```text
inputs
stored_data
calculated
outputs
```

### Step 4 — Update Hodos methodology

Update `hodos.md` to describe the new flow:

```text
brainseed
  → extract-spec
  → normalize-spec
  → resolve-spec
  → slice-work
  → plan-one-slice
  → critic
  → execute
  → verify
```

Make clear that this is for Big-tier or muddy Medium-tier work, not every task.

### Step 5 — Update project CLAUDE fragment

Update `CLAUDE-add-to-project.md` with:

- Artifact anchor rule
- Spec CSV rules
- Table meaning preservation rule
- Instruction not to change CSV structure unless explicitly working on table structure
- Instruction to stop if spec/code/table conflict appears

### Step 6 — Leave skill synchronization for a later pass

Do not fully rewrite skills yet. Add TODOs or open questions if needed.

## Completion Criteria

This planning concept is ready to implement when:

- The database field table shape is accepted.
- The interaction matrix concept is accepted.
- The first-pass CSV rules are accepted.
- The location of table docs is accepted.
- The project has a clear choice on blank templates vs example templates.

Implementation is complete when:

- The `software-project/` template contains the new spec-table documentation.
- The template includes seed CSV structures for database fields and page interaction matrices.
- Hodos documentation reflects the specification-convergence flow.
- Skill changes are either explicitly deferred or captured as TODO/open questions.
