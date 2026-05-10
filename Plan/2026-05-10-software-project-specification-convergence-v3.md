# Plan: Tighten Hodos Specification Convergence

## Status

Superseded by `2026-05-10-software-project-specification-convergence-v4.md`. This version is retained only as the prior active concept artifact.

## Purpose

Capture the working direction for modifying `software-project/` so Hodos has a stronger specification-convergence layer between loose human brain dumps and implementation code.

This is not implementation yet. It is the saved artifact for the concept so the work does not depend on chat context.

## Core Problem

The current Hodos/Superpowers-style flow is useful, but it lets loose narrative become an implementation plan too quickly.

Current rough flow:

```text
brainseed / brainstorm
  → plan
  → critic
  → execute
```

The weakness is the missing layer between:

```text
human intent
```

and:

```text
implementation-ready specification
```

Planning is currently being asked to do too many jobs at once:

- Extract requirements
- Resolve ambiguity
- Detect contradiction
- Define behavior
- Decompose work
- Sequence implementation
- Set acceptance criteria
- Bound agent scope

That makes plans either too broad or too vague. When execution begins, the coding agent still has to make product/design decisions. On small programs this works. On larger programs it causes drift.

## Core Direction

Hodos should add a specification-convergence layer before planning.

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

## Spec Canvas

The key new concept is the **spec canvas**.

A spec canvas is a shared human/AI editing artifact.

The user edits it to communicate intent. The AI normalizes it into standard software terminology and structure. The artifact remains close enough to the generated JSON model that it can round-trip without hidden meaning.

In first-pass Hodos, the spec canvas should usually be a CSV table.

## CSV and JSON Authority Model

The CSV and JSON do different jobs.

```text
CSV = human-facing spec canvas
JSON = normalized AI/code execution model
Markdown = review/report view
Code = implementation
```

The CSV is the user's reviewed source of intent.

The JSON is the AI's executable interpretation.

The CSV is not merely an export. It is the primary human review/edit surface.

The JSON is not discarded. It exists because AI and code generation work better from a normalized structure.

## Round-Tripping Rule

The CSV canvas should be round-trippable.

```text
CSV → JSON → CSV
```

Round-tripping does not require identical wording or ordering. It requires preserving meaning.

Round-tripping should preserve:

- Fields
- States
- Actions
- Validation
- Relationships
- Behavior
- Open questions
- Decisions
- Included and excluded scope

The regenerated CSV should look like a table projection of the JSON model. The user should not get used to a special spreadsheet format that the AI cannot regenerate from JSON.

## Meaning Preservation Rule

The AI may freely improve:

- Spelling
- Terminology
- Naming
- Row labels
- Column labels
- Ordering
- Formatting consistency
- Alignment with codebase terminology

The AI may not silently change:

- Behavior
- Requirements
- Field meaning
- State meaning
- Action meaning
- What is locked/editable
- What is required/optional
- What is included/excluded
- Accepted decisions

Core rule:

```text
Terminology can converge. Meaning must not drift.
```

## Semantic Diff Rule

When the AI normalizes a CSV canvas, generates JSON, or regenerates a CSV from JSON, it should produce a semantic diff when the change is meaningful.

The semantic diff should distinguish:

- Meaning-preserving wording cleanup
- Structural normalization
- Generated values
- Unresolved ambiguity
- Proposed meaning changes requiring approval

Example categories:

```text
Renamed for terminology:
- “marshmallow state” → “pending_review”

Preserved behavior:
- Fields remain locked in this state.

Needs decision:
- Row 14 still says `tbd` for validation.

Requires approval:
- Proposed changing `editable` to `locked` for `project_name` in `submitted` state.
```

## Spec CSV Philosophy

Do not over-specify rigid CSV schemas up front.

The preferred workflow is:

```text
AI generates conventional, useful CSV/table views.
User edits them as a clearer communication surface.
AI interprets the edited tables.
AI normalizes the result into JSON/Markdown/code-facing artifacts as needed.
```

The point is not perfect database modeling discipline. The point is orders-of-magnitude clearer intent transfer than prose alone.

Even if the table structure is imperfect, user edits attached to specific rows and columns are far clearer than a prose explanation.

## Minimal User-Facing CSV Rules

The rules should remain simple.

Primary rule:

```text
If adding a row or column, fill the cells with explicit values so the CSV remains structurally readable.
```

Supporting rules:

- Keep one header row.
- Keep every row at the same column count.
- Do not leave blank cells when adding new rows/columns.
- Use explicit values such as `none`, `n/a`, `unknown`, or `tbd`.
- Do not rely on color, formatting, filters, or formulas for spec meaning.
- Color/formatting may be used only as human visual cues.
- Avoid formulas in authoritative spec CSVs.
- Avoid multi-line cells where practical.

## Specification Convergence Flow

### Brainseed

Purpose: capture messy human intent freely.

Brainseed can include long voice-dictated narrative, uncertainty, examples, contradictions, and reasoning. It is raw material, not contract.

Rule:

```text
Brainseed is context, not authority.
```

### Extract Spec

Purpose: convert narrative into candidate structured objects or candidate spec canvases.

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

Purpose: clean up extracted objects or CSV canvases before the user reviews them.

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
- Linked spec objects or canvases
- Relevant local code context
- Acceptance criteria
- Explicit exclusions

Distinction:

```text
Spec says what must be true.
Slice says what one agent should change now.
Plan says how that slice will be changed.
```

## Database Spec Canvas

Database specs should usually be represented as CSV canvases.

Working direction:

- Prefer one CSV per database table when useful.
- Do not force the entire database into one giant CSV.
- Let AI generate conventional table views from the project or brain dump.
- Let the user edit those tables.
- Let AI interpret the edits and normalize them into JSON/Markdown/code-facing artifacts.

Example candidate structure:

```text
software-project/specs/database/
  README.md
  contacts.csv
  addresses.csv
  phone_numbers.csv
  emails.csv
  relationships.csv
```

The exact structure should be generated based on the project. Hodos should not require a rigid universal schema before the project is understood.

## Related Tables and Logical Data Structures

A logical data structure may include multiple database tables.

Example:

```text
Address book
  contacts
  addresses
  phone_numbers
  emails
```

The AI should be able to generate related table canvases and describe relationships such as:

- One contact can have many phone numbers.
- One contact can have many addresses.
- One contact can have many emails.

This can be captured in a `relationships.csv` or similar generated canvas when useful.

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

## Interaction Matrix Row Order

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

## Interaction Matrix Columns

Rows are page items. Columns describe identity, state behavior, actions, validation, and notes.

Possible columns:

```text
section,item_id,label,item_type,data_source,destination,state_draft,state_submitted,state_approved,on_load,on_change,on_save,on_submit,validation,notes
```

The exact columns should be generated or refined based on the page.

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

## Page Mock-Up Canvas

Add a lightweight page mock-up step before implementation for user-facing screens.

Purpose:

```text
Give the user something visual to react to before the interaction matrix, plan, or code becomes too invested.
```

This should be quick and dirty by design. The goal is not polished UI design. The goal is fast visual convergence.

Preferred first-pass artifact:

```text
software-project/specs/pages/<page-name>/mock-up.html
```

Possible supporting artifacts:

```text
software-project/specs/pages/<page-name>/mock-up-notes.md
software-project/specs/pages/<page-name>/interaction-matrix.csv
```

The mock-up can be a bare HTML file that opens in a browser. It should use simple static markup and minimal styling. It should not require a frontend build system unless the project already has one.

The AI should generate the first draft from the user's description or from an existing project page. The user should be able to say things like:

```text
Make the customer form less cramped.
Move the totals above the line items.
Show me a version with the filters on the left.
This page needs a big approval button, but only after review is complete.
```

The AI then regenerates the mock-up quickly.

This is the UI equivalent of the CSV spec canvas:

```text
AI generates a simple visual canvas.
User reacts to the canvas.
AI normalizes the page structure and behavior into specs.
```

The mock-up should intentionally avoid high-effort polish because polish slows iteration. If the AI spends too much effort making it beautiful, the user becomes less likely to ask for changes.

Reason:

```text
The user can tell almost instantly from a mock-up whether the page has the right data, rough layout, and navigation intent.
```

This prevents the common failure mode where the AI makes a massive implementation change, then reveals a working page that missed the point entirely.

Guidelines:

- Use plain HTML first unless the target project already has a UI framework.
- Keep the page easy to regenerate.
- Prefer static mock data.
- Prefer obvious layout over polished styling.
- Do not wire real backend behavior.
- Do not treat mock-up code as production code.
- Do not let visual polish hide unresolved behavior.
- Use the mock-up to drive or validate the page interaction matrix.
- Give visible sections, boxes, fields, and action areas stable labels or IDs so the user and AI can refer to them.
- Prefer realistic sample values over empty boxes.
- Make navigation areas visible even if links do not work.
- When layout feedback is being discussed, return to the mock-up instead of iterating production UI directly.

Relationship to other artifacts:

```text
mock-up.html = visual page canvas
interaction-matrix.csv = behavior/data canvas
JSON model = normalized AI/code model
implementation = production code
```

The mock-up answers:

```text
What should the page roughly look like and how should the user experience flow?
```

The interaction matrix answers:

```text
What data, states, actions, validation, and stored effects does this page have?
```

Both are needed for large or ambiguous UI work. The mock-up lets the user react visually. The matrix turns that reaction into implementation-ready structure.

Bidirectional workflow:

```text
Description → mock-up.html → user feedback → revised mock-up.html → interaction-matrix.csv → implementation
Existing page → reverse-engineered mock-up.html → user feedback → revised mock-up.html → implementation changes
```

The AI should be able to inspect an existing working page and reverse-engineer a simple mock-up from it. This lets layout iteration move back to the cheap visual canvas instead of happening directly in production UI code.

Industry-adjacent terms:

- Page mock-up
- Wireframe
- Low-fidelity mockup
- Storyboard
- Prototype
- Clickable mockup

Preferred Hodos term:

```text
page mock-up
```

Use `wireframe` as a secondary term when talking to software/UI people. Use `storyboard` only as an analogy for sequencing or page flow.

## Vision Convergence

The existing `vision/` folder remains important, but it needs a convergence rule similar to specs.

Problem:

```text
Raw user explanation is valuable, but long voice-dictated context can become a massive ambiguous vision file.
```

The user may explain the same project differently on different days because the thinking has evolved. Monday's framing may not completely align with Thursday's framing. That is normal. The AI should not blindly paraphrase every explanation into permanent vision text.

Vision should preserve project memory without becoming a confusing rulebook.

Preferred model:

```text
raw capture = what the user said
points of consideration = notable ideas, risks, tradeoffs, and concerns extracted from the raw capture
distilled vision = cleaned current understanding of purpose, users, use cases, and direction
decision context = why important choices were made
potential directions = ideas worth revisiting later
rejected ideas = explicit negative scope and ruled-out directions
open tensions = unresolved or conflicting ideas that should not be silently resolved
```

Candidate structure:

```text
software-project/vision/
  README.md
  raw/
    <date>-<topic>.md
  points-of-consideration.md
  potential-directions.md
  product-brief.md
  users-and-use-cases.md
  decision-context.md
  rejected-ideas.md
  open-tensions.md
```

This structure is a candidate, not a rigid requirement.

Rules:

- Do not treat raw rambling as final project direction.
- Do not paraphrase every user sentence into vision.
- Extract durable ideas from rambling.
- Treat rambling as triage material, not as a document to preserve verbatim by default.
- If the user thinks through an idea and rejects it, capture the rejection concisely.
- Treat rejected ideas as useful negative scope, but not as normal spec-level context.
- Keep rejected ideas separate from distilled vision so they do not bloat the current path.
- Load rejected ideas mainly for broad brainstorming, major direction changes, or when revisiting product scope.
- Put unresolved or maybe-later ideas into potential directions instead of current vision.
- Promote only rock-solid settled direction into distilled vision.
- Keep outdated ideas as historical context or open tensions, not current truth.
- Distill repeated themes into concise current vision.
- Preserve decision context when it explains why the product has its shape.
- Mark contradictions or changed thinking explicitly.
- Ask before promoting a passing thought into stable project direction.

The AI should be able to take raw notes and produce:

```text
New points of consideration
New potential directions
Updated distilled vision
New or changed decision context
Rejected ideas worth recording
Open tensions / contradictions
Suggested deletions or demotions from current vision
```

Vision triage statuses:

```text
accepted-current = belongs in distilled vision now
accepted-later = likely valuable, but version 2/version 3/future
explore = worth discussing later, not decided
rejected = considered and ruled out
superseded = once useful, replaced by newer thinking
unresolved = conflict or ambiguity remains
```

The normal output of a rambling discussion should be a short triage result, not a transcript:

```text
Conclusion:
- [settled decision]

Potential directions:
- [future idea worth revisiting]

Rejected:
- [ruled-out direction and short reason]

Open tension:
- [only if still unresolved]
```

Important distinction:

```text
Vision prevents purpose drift.
Specs prevent implementation drift.
```

Vision may influence judgment, naming, prioritization, and user experience. Vision must not silently override specs.

If a vision change implies changed behavior, it must be promoted into `specs/` through the spec convergence workflow.

Rejected ideas are different from specs:

```text
specs = what the software must do
distilled vision = why this path is the current good path
rejected ideas = broad context for what was considered and ruled out
```

Rejected ideas should usually live in their own file. They are not normally needed when moving a field, refining one page, or making a small implementation change. They become relevant when the project is being rethought at a larger scope.

## Existing Project Workflow

A future Hodos workflow should support applying this to an existing project.

Desired capability:

```text
User asks AI to inspect an existing project.
AI generates database/table canvases.
AI generates page interaction matrices.
User edits the CSV canvases.
AI interprets the edits.
AI normalizes to JSON/Markdown if needed.
AI slices implementation work.
AI plans and executes one slice at a time.
```

The important behavior is that the user does not have to manually create the CSVs from scratch. The AI should generate useful first drafts.

## Candidate Directory Structure

This is a candidate direction, not a rigid final structure:

```text
software-project/
  specs/
    database/
      README.md
      <table-name>.csv
      relationships.csv
    pages/
      README.md
      <page-name>/
        mock-up.html
        interaction-matrix.csv
    generated/
      README.md
      model.json
      reports.md
```

Generated files should eventually be clearly marked. CSV canvases should be treated as the human-facing source of intent.

## Critic Questions To Add Later

The critic should not only ask whether a plan is plausible. It should ask:

- Is the spec tight enough that multiple fresh agents would implement the same behavior?
- Are any table cells `unknown` or `tbd` in a way that blocks implementation?
- Is the interaction matrix page-specific, or did it accidentally pull in unrelated system scope?
- Is there enough page mock-up information for the user to react visually before implementation?
- Is the mock-up still low-fidelity enough to iterate quickly?
- Did the mock-up reveal page behavior that must be added to the interaction matrix?
- Are visible regions, boxes, fields, and actions labeled well enough for the user and AI to discuss them?
- Is raw vision being distinguished from distilled current vision?
- Are outdated or conflicting vision ideas marked as historical or open tensions rather than current direction?
- Are row meanings preserved after terminology cleanup?
- Are actions and states represented explicitly enough?
- Is the slice small enough for one coding agent?
- Does the plan require the executor to make product decisions?
- Did CSV-to-JSON normalization produce a semantic diff?
- Are proposed meaning changes clearly separated from wording cleanup?

## Skill Changes Deferred

Skill work is the next major topic, but this plan does not implement it yet.

Likely future skills:

- `extract-spec`
- `resolve-spec`
- `slice-work`
- project inspection / canvas generation
- table consistency checks
- semantic diff / round-trip check

For now, this plan captures the artifact model and process direction. Skills should be updated after the artifact model is accepted.

## Open Questions

These should be resolved before implementation:

- Where should the spec canvas rules live: `specs/README.md`, `hodos.md`, `CLAUDE-add-to-project.md`, or a new dedicated file?
- Should the template include blank CSV templates, fake example CSVs, or both?
- How much structure should be provided versus generated per project?
- Should generated JSON be implemented now or deferred as a documented future step?
- What is the minimum useful semantic diff for CSV/JSON round-tripping?
- Should page mock-ups be plain HTML only, or should projects be allowed to use their native frontend framework for mock-ups?
- Should the template include one fake `mock-up.html` example under `specs/pages/`?
- Should `vision/` include a `raw/` folder, or should raw captures live in `notes/` or `brainseed/` instead?
- What is the smallest useful set of distilled vision files?
- Should the existing v2 plan be kept, superseded, or deleted after this v3 is accepted?
- Should the broken partial `2026-05-10-software-project-specification-hole.md` be marked superseded?

## Implementation Plan Candidate

### Step 1 — Mark old artifacts clearly

Mark the partial original plan as superseded or incomplete.

Keep this v3 file as the current concept artifact unless replaced by a later version.

### Step 2 — Add spec canvas documentation

Update the template documentation to explain:

- Spec canvas
- CSV as human-facing intent surface
- JSON as normalized AI/code model
- Round-tripping
- Semantic diff
- Meaning preservation
- Minimal CSV discipline

Likely files:

```text
software-project/specs/README.md
software-project/hodos.md
software-project/CLAUDE-add-to-project.md
```

### Step 3 — Add seed folders and examples

Add candidate seed folders such as:

```text
software-project/specs/database/
software-project/specs/pages/
```

Include README files and possibly small fake/example CSVs.

Do not overbuild rigid schemas yet.

Include a small low-fidelity `mock-up.html` example if useful.

### Step 4 — Update Hodos methodology

Update `hodos.md` to describe the specification-convergence flow.

Make clear that this is for Big-tier or muddy Medium-tier work, not every task.

### Step 5 — Tighten vision guidance

Update the vision documentation to separate raw capture from distilled current vision, points of consideration, decision context, and open tensions.

### Step 6 — Defer skill synchronization

Do not fully rewrite skills yet. Capture TODOs or open questions if useful.

## Completion Criteria

This concept is ready to implement when:

- The spec canvas concept is accepted.
- CSV as human-facing truth is accepted.
- JSON as normalized execution model is accepted.
- Round-tripping and semantic diff are accepted.
- The first-pass locations for database canvases and page interaction matrices are accepted.

Implementation is complete when:

- The `software-project/` template documents spec canvases.
- The template includes seed locations/examples for database canvases, page mock-ups, and page interaction matrices.
- Vision documentation distinguishes raw capture from distilled current vision and decision context.
- Hodos documentation reflects the specification-convergence flow.
- Skill changes are explicitly deferred or captured as TODO/open questions.
