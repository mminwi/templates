# Plan: Hodos Software Project Workflow

## Status

Active draft / not yet implemented

## Purpose

Define the workflow for building and maintaining the `software-project/` template using the product specification model captured in `2026-05-10-software-project-specification-convergence-v4.md`.

This plan should not restate the full product specification model. It should define how an AI session moves through the artifacts during real work.

## Relationship To Product Specification Plan

Product specification plan:

```text
2026-05-10-software-project-specification-convergence-v4.md
```

That plan defines what Hodos is trying to produce:

- Vision triage
- Spec canvases
- Database CSVs
- Page mock-ups
- Interaction matrices
- JSON/Markdown generated views
- Semantic diff and round-tripping rules

This workflow plan defines how the AI should use those artifacts:

- What to read first
- What to generate first
- When to ask the user
- When to stop
- When to plan implementation
- How to handle existing projects versus new projects

## Workflow Implementation Target

The primary workflow implementation target should remain Claude-compatible skills and commands:

```text
software-project/.claude/skills/
software-project/.claude/commands/
software-project/hodos.md
software-project/CLAUDE-add-to-project.md
```

Do not make Windsurf workflows the canonical workflow layer yet. Windsurf may be used to help edit, test, and implement the template, but the durable workflow should remain portable to Claude Code because Claude Code is expected to remain a major coding tool.

Goal:

```text
Refactor the existing Hodos skills around the new product specification model instead of adding a large parallel workflow system.
```

## AI-First Code Comprehension Assumption

The workflow should assume the user is not planning to investigate code manually.

Instead, the expected pattern is:

```text
AI inspects code
  → AI reverse-engineers current behavior into canvases
  → user reviews CSVs and page mock-ups
  → AI identifies drift/gaps
  → AI updates specs/plans/code
```

This changes the purpose of the artifacts. They are not primarily for a human programmer reading the code. They are for a human owner to quickly validate AI understanding without opening the implementation.

Important implications:

- The AI should generate database CSVs, page mock-ups, and interaction matrices from existing code when asked.
- The user should be able to review the canvases instead of reading source files.
- Frameworks and abstractions should be judged by whether they help AI maintain the system, not whether they make manual programming more pleasant.
- For layout discussions, return to `mock-up.html` rather than iterating production UI code directly.
- For data/behavior discussions, return to CSV canvases rather than relying on prose.

## Framework And Library Selection Rule

The template should avoid frameworks that primarily benefit human programmers while making the codebase harder for AI to understand in one context.

Working hypothesis:

```text
Every framework adds conceptual surface area the AI must account for.
```

This does not mean frameworks are always bad. Widely used frameworks may be familiar to AI models because they are heavily represented in training data. A framework or library may also reduce custom code, provide proven behavior, improve security, or move performance-critical work into tested code.

The selection test should be:

```text
Use a framework/library only when it improves the AI-maintainability or reliability of the system more than it increases conceptual load.
```

Good reasons to use a library or framework:

- It provides proven, tested behavior.
- It avoids writing complex custom code.
- It handles security-sensitive or performance-sensitive work better than bespoke code.
- It is already native to the target project stack.
- It is common enough that AI tools understand its conventions reliably.
- It reduces total code and cognitive load for the AI.

Bad reasons to use a framework:

- It mainly organizes code for human convenience.
- It hides simple behavior behind a large abstraction.
- It forces workaround code because the framework oversimplifies a real requirement.
- It expands the context the AI must understand without reducing implementation complexity.
- The user will not personally benefit from the framework because the user is not manually programming against it.

The AI should make framework recommendations explicitly, including:

```text
What the framework/library buys us
What conceptual load it adds
Whether AI tools are likely to understand it well
What custom code it prevents
What workarounds it may force
Recommendation: use / avoid / defer
```

## Engineering Notebook Rule

Any time the workflow uses a plan file, the engineering notebook is engaged too.

Rule:

```text
If the work is significant enough for `write-plan`, it is significant enough for a notebook entry.
```

The notebook is not for legal/IP protection in this template. It is the trace record for what was attempted, why, how, and what happened.

Candidate folder:

```text
software-project/notebook/
```

Notebook entries should capture:

- What we are doing
- What we hope to achieve
- Why this work matters
- What process/approach we are using
- What artifacts were used
- What result occurred
- What was learned
- What follow-up remains

The plan and notebook answer different questions:

```text
plan = what tasks will be executed
notebook = what happened and what was learned
```

Workflow implication:

```text
brainstorm
  → update notebook with goal/context/decomposition summary
write-spec
  → update notebook with spec/canvas summary and notable decisions
write-plan
  → create or link notebook entry with plan overview, not full plan text
execute-plan
  → update notebook with important results, surprises, and conclusion
```

The notebook entry may start short when the plan is written, then be completed after verification/review.

Notebook entries should not duplicate the full content of plans, specs, CSVs, or mock-ups. Those artifacts remain in their own folders and should be linked from the notebook.

The notebook should capture summaries and context that may not be obvious from the structured artifacts:

- Why this work was started
- What problem was observed
- What equipment, environment, customer, data source, file, device, or external constraint mattered
- What was expected to happen
- What approach was chosen and why
- What non-obvious facts were discovered
- What failed or surprised the team
- What result was achieved
- What should be remembered later

Example:

```text
If the project is debugging a storage issue and the relevant device is a Western Digital drive, record that fact in the notebook even if no source file contains it.
```

The notebook should be substantial over the life of a project, but each entry should summarize the stage rather than paste every underlying artifact.

## Bounded Loop And Escalation Rule

Workflow loops should be bounded.

The AI should not cycle indefinitely through critic, review, revise, and re-check loops. Most loops should run one or two iterations and then escalate.

Default rule:

```text
Attempt automatic repair/review loops no more than two times before escalating.
```

Escalation path:

```text
worker/builder agent
  → reviewer/critic agent
  → orchestrator/composer agent
  → user, only if unresolved or decision requires owner judgment
```

When escalating, the AI should provide:

- What failed
- What was attempted
- Why the loop did not resolve it
- Whether the issue appears to be scope, interface, missing context, unclear spec, implementation error, or test failure
- Recommended next action

The user should not be asked to manually manage routine retry loops. The user should be brought in when the orchestrator cannot resolve the issue, when the decision is a product/business judgment, or when continuing would require changing approved scope.

## Intake Classification And Tier Rule

Intake classification means the AI decides what kind of work the user is asking for before choosing the workflow.

Plain-language rule:

```text
Before acting, classify the request so the AI does not use a heavyweight workflow for a tiny change or a lightweight workflow for risky work.
```

Recommended intake classes:

| Class | Meaning | Default workflow |
|---|---|---|
| Tiny change | Typo, small copy edit, obvious one-line correction | Do it, verify briefly, no plan/notebook |
| Small implementation | Bounded code change with little ambiguity | Update spec only if behavior changes, implement, verify |
| Spec/product change | Behavior, page, data, or product intent changes | Brainstorm if unclear → update spec canvases → plan |
| Medium build | One coherent feature or page with several files | Brainstorm → update spec → write plan/notebook/test plan → execute/review |
| Big build | Multi-page, multi-module, schema, architecture, or multi-agent work | Full workflow with decomposition, composer, critic, skeleton phase, test plan, notebook, execute/review |
| Existing project reverse engineering | User wants AI to understand current code | AI inspects code → generate canvases/mock-ups → user reviews → gaps/drift become spec/product changes |
| Troubleshooting/debugging | Something is broken or unknown | Use maintenance/debug SOP, then update notebook/spec/test plan if the finding matters |
| Refactor/cleanup | Behavior should stay the same | Characterize behavior/tests first → plan if non-trivial → execute/review |

Tier rule means the amount of process should match the risk and size of the work.

Recommended tiers:

| Tier | Use when | Required process |
|---|---|---|
| Tier 0 — Tiny | Safe edit; no meaningful behavior change | Direct edit + quick verification |
| Tier 1 — Small | Local behavior/code change; low risk | Lean implementation + verification; update spec/test if behavior changes |
| Tier 2 — Medium | Several files, one feature/page, meaningful behavior | Brainstorm if needed → spec canvases → plan/notebook/test plan → execute with reviewer |
| Tier 3 — Big | Multi-agent, multi-module, schema, architecture, high risk | Full workflow: brainstorm/decompose → spec canvases → composer → plan + critic loop + skeleton + test plan + execute + separate reviewer |

The AI should recommend the tier and workflow at the start of the work. The user should not need to know the tier labels.

## Spec Revision Convention

Use the common software convention:

```text
current canonical file stays at the stable path
meaningful changes are recorded by Git plus an adjacent changelog/revision note
```

Default rule:

```text
Do not create endless v1/v2/v3 copies of every CSV or mock-up during normal work.
```

Instead:

- Keep the current spec artifact at the stable path.
- Use Git for exact line/file history.
- Record meaningful conceptual revisions in a nearby changelog or revision section.
- Only fork a full versioned artifact when comparing two materially different alternatives or preserving a major baseline.

Example:

```text
specs/pages/contacts/interaction-matrix.csv
specs/pages/contacts/mock-up.html
specs/pages/contacts/CHANGELOG.md
```

For database tables:

```text
specs/database/contacts.csv
specs/database/CHANGELOG.md
```

The changelog should summarize what changed and why, not duplicate the full CSV.

## Test Plan Format

Default test plan:

```text
tests/TEST_PLAN.md
```

Recommended format:

```markdown
# Test Plan

## Purpose

## How To Run The Test Suite

## Current Regression Suite

| Area | Test/Check | Type | Command/Location | Expected Result | Status |
|---|---|---|---|---|---|

## Current Change Additions

| Change/Plan | New Test/Check | Type | Command/Location | Expected Result | Keep As Regression? |
|---|---|---|---|---|---|

## Manual Verification Checks

| Area | Check | Expected Result | Last Verified |
|---|---|---|---|

## Known Gaps

| Gap | Risk | Proposed Follow-up |
|---|---|---|
```

The AI reads and maintains this more than the user. It should be concise enough to scan but complete enough that future agents know which tests matter.

## Notebook Format

Default notebook folder:

```text
notebook/
```

Recommended entry path:

```text
notebook/{YYYY-MM-DD}-{short-topic}.md
```

Recommended format:

```markdown
# Notebook — {Topic}

**Date:** {YYYY-MM-DD}
**Related plan:** {link}
**Related specs:** {links}
**Related tests:** {links}

## Objective

## Expected Outcome

## Context Not Captured Elsewhere

## Process Summary

## Stage Notes

### Brainstorm

### Spec Update

### Planning

### Execution

### Verification

## Result

## Lessons / Follow-up
```

The notebook is mostly for future AI and future traceability. It should summarize and link; it should not paste entire plans/specs/tests.

## Composer, Critic, And Reviewer Boundaries

### Composer

The composer is the coordinating programmer/orchestrator for multi-agent or decomposed work.

Composer responsibilities:

- Decide how to divide work into slices.
- Assign slices to agents with clear file/page ownership.
- Decide which slices can run in parallel and which must run in series.
- Define interfaces between slices before work begins.
- Receive completed slices back from agents.
- Check that the returned pieces still fit together.
- Make small integration adjustments when needed.
- Escalate if integration requires changing scope, spec, or major architecture.

The composer is allowed to make minor glue/integration changes so the pieces function together. The composer should not silently change product behavior or approved scope.

### Reviewer

The reviewer checks execution against a specific plan or slice.

Reviewer question:

```text
This is what the agent was told to do. Did it actually do it?
```

The reviewer should have a short, focused context window:

- The plan or slice instructions
- Relevant spec/canvas artifacts
- Files changed by the worker/composer
- Relevant test plan entries

The reviewer should not need broad vision context unless the plan itself references it. The reviewer's job is mostly binary and concrete: done / not done / unclear.

### Critic

The critic checks the broader design or plan before implementation.

Critic question:

```text
If this plan/spec is executed, will the end result actually work and realize the intended product outcome?
```

The critic is broader and harsher than the reviewer. The critic looks for:

- Missing edge cases
- Contradictions
- Bad assumptions
- Integration risks
- Architecture problems
- Happy-path failure
- Mismatch between vision/spec intent and planned result

### Boundary summary

```text
composer = do the pieces fit together?
reviewer = did the assigned work get done?
critic = is the planned/design outcome actually good enough?
```

If the check is against a CSV, mock-up, interaction matrix, plan checkbox, or test plan entry, it is usually reviewer work.

If the check asks whether the whole direction, architecture, or product outcome will actually succeed, it is critic work.

## Definition Of Done

A non-trivial plan is not done until all of the following are true:

- The approved plan tasks are complete.
- A separate reviewer verifies the work against the plan.
- The implementation matches the relevant CSVs, mock-ups, interaction matrices, and state tables.
- The test plan was updated.
- Required tests were added or updated.
- Required tests were run, or manual checks are explicitly recorded.
- Skeleton/framework structure was respected if the plan required it.
- The notebook was updated with result, surprises, and follow-up.
- Any deviations from the approved plan/spec are documented and approved or escalated.

Short rule:

```text
Done means a separate short-context reviewer can verify that the plan was executed, not that the builder believes it is close enough.
```

If anything is still in question, the workflow should not mark the plan complete. It should escalate with a recommendation.

## Human Escalation Points

The user should be pulled in only when owner judgment is needed or automated loops have failed.

Escalate to the user when:

- Two automatic critic/revision or reviewer/fix loops fail.
- The orchestrator/composer cannot resolve the issue.
- The approved scope needs to change.
- Product behavior is ambiguous.
- Vision or business direction is affected.
- A framework/library choice materially changes complexity or long-term maintainability.
- The agents disagree about whether the result satisfies the spec.
- The next step requires accepting known risk.

When escalating, the AI must provide a recommendation:

```text
Issue:
What we tried:
Why it is still unresolved:
Options:
Recommendation:
Consequence of recommendation:
```

## Recommendation Quality Rule

The AI should provide recommendations, but should not over-recommend.

Rule:

```text
Prefer one strong recommendation over three padded options.
```

When there is one clearly best path, recommend that path and explain why.

Do not present multiple options just to satisfy a formula. Extra options are harmful when they are weak, artificial, or obviously worse. They make the user choose between paths the AI should have already filtered.

Present multiple options only when there are genuinely distinct viable paths with meaningful tradeoffs.

If an option is technically possible but not recommended, either omit it or mention it briefly as rejected:

```text
Rejected: {option} — not recommended because {reason}.
```

Good recommendation behavior:

- State the recommended path.
- Explain the reason.
- Call out material tradeoffs or risks.
- Mention rejected paths only when they clarify the decision.
- Ask the user only for judgment calls the AI cannot make.

Bad recommendation behavior:

- Always giving three options.
- Padding with weak options.
- Making the user choose between options the AI already knows are poor.
- Being neutral when one option is clearly best.
- Hiding the recommendation behind a list of possibilities.

## Skill Refactor Direction

The existing Hodos skill set should be simplified rather than expanded.

Target direction:

```text
brainstorm
  → write-spec
  → write-plan
      → include skeleton/framework tasks when substantial
      → create/update test plan
  → execute-plan
      → mandatory separate-agent verification/review before completion
```

### Keep `brainstorm`, fold `decompose` into it

Keep `brainstorm` as the opening convergence skill.

`decompose` should not remain a separate required step for normal work. Decomposition should happen inside brainstorming when the work is too large to plan as one unit.

Brainstorming should produce:

- The goal
- The major sections/slices of work
- A recommended slice size and agent count
- Interfaces between slices
- Shared data files/contracts between slices
- Which slices can be handled independently or by multiple agents
- Which artifacts are needed before planning
- Whether critic review should run automatically
- Whether skeleton/framework-first should be required

The output of brainstorming should make it clear which section is being planned next:

```text
Brainstorm result
  → Section A plan
  → Section B plan
  → Section C plan
```

The sections do not always need to be sequential if the interfaces and shared contracts are clear.

Default decomposition preference:

```text
Prefer more small coherent slices over fewer large slices.
```

The goal is to keep each agent's working context small enough that it does not need to pull in the entire project, broad vision material, or unrelated implementation details. Two or six slices is usually better than one large slice if the interfaces can be stated clearly.

Brainstorming should give the composer guidance such as:

```text
Recommended decomposition: 4-6 small implementation slices
Recommended parallelism: 2-3 agents
Integration risk: low / medium / high
Shared interfaces that must be stable before parallel work begins: ...
```

The composer may override this recommendation if the project structure makes the suggested split artificial, unsafe, or more complex than the work itself.

### Composer role after decomposition

When brainstorming decomposes work into multiple slices, a composer role should check that the pieces still integrate before the plan goes to critic review.

Composer responsibilities:

- Confirm each slice has a clear boundary.
- Confirm each slice has the context it needs and no unnecessary context.
- Confirm shared files, APIs, CSVs, schemas, and page contracts are named.
- Assign one owner per writable page/file/spec artifact.
- Prevent multiple agents from modifying the same file in parallel.
- Confirm the planned slices reassemble into the original goal.
- Identify interface conflicts before implementation begins.
- Decide whether slices can proceed in parallel or must be sequenced.
- Decide whether the brainstorm-recommended number of agents/slices should be changed.

Composer checkpoint:

```text
brainstorm/decompose
  → draft slice plans/spec directions
  → composer integration check
  → revise slices/interfaces if needed
  → write-plan / critic-review
```

The composer is not the same as the critic. The composer asks, "Do the pieces fit together?" The critic asks, "What is wrong, missing, risky, or contradictory?"

### Parallel work ownership rule

Parallel decomposition should optimize for clear file/page ownership.

Rule:

```text
No two agents should be assigned to modify the same file, page mock-up, CSV canvas, or implementation module in parallel.
```

Preferred slice boundaries:

- One page or small page group
- One database table or small related table group
- One interaction matrix
- One state/transition table
- One implementation module with clear public interfaces
- One test file or test group owned by the same slice

If two slices need the same file changed, the composer should choose one of these options:

- Sequence the slices so only one agent modifies the shared file at a time.
- Keep the slices small even if they must run in series.
- Extract a shared interface/spec change first.
- Assign the shared file to a single owner and make other agents read-only consumers.
- Merge the slices only if sequencing would create more complexity than it removes.

Shared writable files do not prohibit decomposition. They prohibit parallel execution of those particular slices.

The goal of decomposition is not just smaller work. The goal is smaller work that can be safely assigned with the correct execution mode:

```text
slice = clear objective + clear context + clear owned files + clear interfaces + parallel/series decision
```

### Rewrite `write-spec` around canvases

`write-spec` should become the skill that creates or revises the specification canvases:

- Database CSVs
- Page interaction matrices
- Page mock-ups
- State/transition CSVs when state behavior matters
- JSON normalized views where useful for AI/code processing
- Markdown review views where useful for human review

The old Mermaid `flowchart` skill should be removed or demoted to an optional diagram helper. Flowcharts were too hard to make complete enough to be the main behavioral artifact. State behavior should usually be captured as a state/transition table or CSV instead.

Spec changes should normally create a new revision rather than silently overwriting the old conceptual version. Git remains the exact history, but the workflow should make meaningful spec revisions visible at the artifact level when the concept changes materially.

Open design point:

```text
Define the exact revision convention for specs/canvases.
```

### Keep `write-plan`

The current `write-plan` behavior is broadly correct and should be preserved:

- Checkbox tasks
- Clear phases/sections
- Files touched
- Done criteria
- Resumable execution
- Plan as execution state

The plan should be written only after the relevant specs/canvases are aligned enough to implement.

When `write-plan` is used, the engineering notebook is also engaged.

### Fold `skeleton-first` into planning

`skeleton-first` should not remain a separate user-called skill in the normal workflow. It should become a required planning pattern for substantial work.

Traditional skeleton/walking-skeleton concepts are useful when the work needs a structural foundation before filling in behavior:

- New modules
- New services
- New pages
- Multi-file architecture changes
- Multiple agents working on related pieces
- Shared interfaces that must be stabilized early

For these cases, the plan should include a skeleton/framework phase before implementation fill-in:

```text
write-plan
  → Phase A: create skeleton/framework
  → Phase B: review/check skeleton shape against specs
  → Phase C: fill in implementation
  → Phase D: run tests and review
```

The skeleton phase may include:

- File/module structure
- Function/class/component signatures
- Route names
- Data contracts
- Empty bodies or minimal placeholders
- Import/export boundaries
- Basic test hooks

The skeleton phase should be skipped for trivial changes where it would add ceremony without reducing risk, such as an isolated eight-line fix.

The user should not need to decide whether skeleton/framework-first applies. The plan writer should decide by rule and include it when the work is substantial enough.

Skeleton review can be performed by another agent/context. The user does not need to manually participate unless the skeleton review finds unresolved ambiguity.

### Planning must create or update the test plan

`write-plan` should also create or update a persistent test plan.

The test plan is not just a one-time checklist for the current change. It is an accumulating regression record of what should continue to work as the project evolves.

Default folder/file:

```text
software-project/tests/TEST_PLAN.md
```

Rule:

```text
Every non-trivial implementation plan should add or update the tests needed to validate the new/changed behavior.
```

Planning should identify:

- New tests required for the current change
- Existing tests that must still pass
- Manual verification steps if automation is not yet practical
- Regression tests that should remain in the suite for future changes
- Edge cases from the spec/canvas artifacts

Tests should not be removed from the test plan just because a specific implementation plan is complete. The goal is cumulative confidence:

```text
It is acceptable to run hundreds of tests at the end of a project if that proves prior behavior still works.
```

### Critic review should be automatic by rule

Critic review belongs inside `write-plan`. It should not be a separate user-called step in the normal workflow.

The user should not have to manually call out that a critic review is needed, and should not have to manually manage each critic iteration.

The workflow should decide during `brainstorm`, `write-spec`, or `write-plan` whether critic review is required. For most non-trivial work, critic review should run automatically before execution.

Default critic loop:

```text
write-plan
  → critic-review
  → revise plan/specs if needed
  → critic-review again if substantive changes were made
  → repeat at most two critic/revision attempts
  → ask user only if still blocked, conflicted, or after two unresolved iterations
```

The critic can be another agent or another AI context. The user's role is to arbitrate unresolved disagreements, not to manually trigger every iteration.

If the critic loop escalates to the user, the AI should provide a recommendation, not just a problem report.

### Skeleton/framework-first should be automatic by complexity

The user should not need to decide when `skeleton-first` is required.

The workflow should require skeleton/framework-first automatically within the plan when the work is large, multi-file, multi-agent, or architecturally risky.

For large work, the flow becomes:

```text
approved plan
  → execute skeleton/framework phase
  → review skeleton shape
  → fill in implementation phases
  → separate-agent verification/review before completion
```

Open design point:

```text
Define hard rules for when skeleton/framework-first is mandatory inside a plan.
```

Testing should be planned before implementation and executed during verification. For substantial work, test hooks or test scaffolding may be created during the skeleton/framework phase.

### Fold verification/review into `execute-plan`

`verify` and `review-work` should not be separate user-called steps in the normal workflow. They should be mandatory final gates inside `execute-plan`.

The core risk this addresses:

```text
AI often completes 80-90% of a task and then claims the work is done.
```

To prevent this, every executed plan must end with a conscious verification/review pass that checks whether the entire approved plan was actually completed.

Default rule:

```text
No non-trivial plan may be marked complete until a separate agent/context verifies the work against the plan, specs, and test plan.
```

The verification/review agent should check:

- Every plan checkbox is complete and supported by actual file changes.
- The implementation matches the spec/canvas artifacts.
- Required skeleton/framework structure was respected.
- Required tests were added or updated.
- The test plan was run or explicitly marked with remaining manual checks.
- No planned work was silently skipped.
- No unplanned files or behaviors were introduced without explanation.
- The notebook was updated with results, surprises, and conclusion.

The builder agent should not be the only reviewer of its own work. A separate agent or fresh AI context is preferred because it has less incentive to rationalize incomplete work.

If verification fails:

```text
execute-plan
  → reviewer identifies gaps
  → builder fixes gaps
  → reviewer re-checks
  → repeat at most two fix/review attempts
  → escalate to orchestrator/composer if still unresolved
  → mark complete only after pass or user-approved exception
```

## Initial Workflow Areas To Refine

### 1. New Project / Brain Dump Workflow

Candidate flow:

```text
brain dump
  → vision triage
  → product brief / users / use cases
  → page mock-ups
  → database CSV canvases
  → page interaction matrices
  → semantic review
  → slice work
  → plan one slice
  → implement
  → verify
```

### 2. Existing Project / Reverse Engineering Workflow

Candidate flow:

```text
inspect existing project
  → generate current-state database canvases
  → generate current-state page mock-ups
  → generate current-state interaction matrices
  → user reviews/edits canvases
  → AI identifies drift/gaps
  → slice work
  → plan one slice
  → implement
  → verify
```

### 3. Page Change Workflow

Candidate flow:

```text
read page mock-up and interaction matrix
  → update mock-up for layout/visual change
  → update interaction matrix for behavior/data change
  → semantic diff
  → plan implementation only after artifacts are aligned
```

### 4. Vision Change Workflow

Candidate flow:

```text
capture rambling
  → triage into current vision / potential directions / rejected ideas / open tensions
  → promote behavior-affecting changes into specs
  → defer broad rejected/context material unless major direction is changing
```

## Open Questions

- Which workflow should be documented first in `software-project/hodos.md`?
- Which rules belong in `CLAUDE-add-to-project.md` versus `specs/README.md` versus `vision/README.md`?
- Should workflows become Claude skills immediately, or should the docs stabilize first?
- What is the minimum workflow that prevents large wrong-direction implementation changes?
- Which existing skills should be merged or rewritten so the workflow has fewer steps?
- Where should the framework/library selection rule live?
- How should AI reverse-engineering into canvases be invoked from natural language?
- Should notebook entries live in `notebook/` or under `plans/` beside the related plan?
- What is the smallest useful notebook entry template?

## Completion Criteria

This workflow plan is ready when:

- New-project workflow is clear.
- Existing-project reverse-engineering workflow is clear.
- Page mock-up iteration workflow is clear.
- Vision triage workflow is clear.
- Claude skills/commands remain the canonical workflow implementation target.
- AI-first code comprehension and reverse-engineered canvases are explicit.
- Framework/library selection is based on AI-maintainability, not human convenience alone.
- Planning and notebook entries are paired in the workflow.
- The boundary between product specification plan and workflow plan remains explicit.
