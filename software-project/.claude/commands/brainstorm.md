---
description: Open non-trivial product/software work, classify the request, converge on direction, decompose into small slices, define interfaces, and hand off to spec canvases
---

# /brainstorm

## When to use

Invoke at the start of work that is not already a tiny, obvious edit:

- New project or new feature
- Existing feature with ambiguous direction
- Page, data, workflow, or behavior change
- Existing-project reverse engineering
- Multi-file or multi-agent work
- Any request where several different implementations could be reasonable

Trigger phrases:

- *"Let's brainstorm…"*
- *"What could we do about X?"*
- *"I'm thinking about X but I'm not sure…"*
- *"Help me figure out the approach for X"*
- *"How should we handle X?"*
- *"Break this up"*
- *"How should we divide this work?"*

Do not use for:

- Tiny safe edits
- Fully specified single-file fixes
- Troubleshooting where the problem is already "something is broken" — use `/troubleshoot`
- Refactors where behavior must stay the same — use `/refactor`

## Core discipline

`brainstorm` is the front door. It decides what kind of work this is, what tier it belongs to, what artifacts are needed, and how to divide the work so later agents can operate with small context windows.

No code is written in this skill.

## Intake classification

Before asking detailed questions, classify the request:

| Class | Meaning | Default handoff |
|---|---|---|
| Tiny change | Typo, copy edit, obvious one-line correction | Do it directly; no Hodos ceremony |
| Small implementation | Bounded low-risk code change | Implement + verify; update spec/test only if behavior changes |
| Spec/product change | Behavior, page, data, or product intent changes | `/write-spec` for spec canvases |
| Medium build | One coherent feature/page touching several files | `/write-spec` → `/write-plan` |
| Big build | Multi-page, multi-module, schema, architecture, or multi-agent work | Full workflow with decomposition, composer guidance, critic, skeleton phase |
| Existing project reverse engineering | User wants AI to understand current code | Inspect code → generate canvases/mock-ups → user reviews gaps |
| Troubleshooting | Something is broken or unknown | `/troubleshoot` |
| Refactor/cleanup | Behavior should stay the same | `/refactor` |

The user should not need to know the tier labels. State your classification briefly and proceed.

## Tier rule

Match process weight to risk:

| Tier | Use when | Required process |
|---|---|---|
| Tier 0 — Tiny | Safe edit; no meaningful behavior change | Direct edit + quick verification |
| Tier 1 — Small | Local behavior/code change; low risk | Lean implementation + verification; update spec/test if behavior changes |
| Tier 2 — Medium | Several files, one feature/page, meaningful behavior | Brainstorm if needed → spec canvases → plan/notebook/test plan → execute with reviewer |
| Tier 3 — Big | Multi-agent, multi-module, schema, architecture, high risk | Brainstorm with decomposition → spec canvases → composer → plan + critic loop + skeleton + test plan → execute + separate reviewer |

## Process

### Step 1 — Read current context

Before asking the user questions, inspect:

- `CLAUDE.md`
- `hodos.md`
- Relevant `specs/`
- Relevant `vision/` only if product purpose or direction is involved
- Existing `plans/`
- Existing `notebook/`
- Existing `tests/TEST_PLAN.md`
- Obvious files related to the request

### Step 2 — Clarify the goal

Ask one question at a time only when the answer is not already available.

Focus on:

- What outcome the user wants
- What problem is being solved
- Constraints
- Success criteria
- Known external context that may not exist in code

Do not ask implementation-detail questions the AI can decide later.

### Step 3 — Research if needed

Before recommending a path, assess whether you know enough to make a strong recommendation. If not, research first.

**When research is needed:**

- Evaluating frameworks, libraries, or tools you haven't worked with in this project
- Comparing architectural patterns for a problem you haven't solved in this context
- The user asks "what's the best way to..." and you don't have a confident answer
- External APIs, services, or integrations you need to understand before recommending
- The problem domain has tradeoffs you can't evaluate without looking at real-world approaches

**How to research:**

- Search the web for current best practices, comparisons, and real-world usage
- Read documentation for candidate libraries/frameworks/APIs
- Check the project's existing dependencies and patterns — the answer may already be in the codebase
- Look at how similar problems are solved in the same stack
- Check `specs/tooling-philosophy.md` if evaluating a new dependency — it has evaluation criteria

**Research output:**

Summarize findings briefly in the notebook (for Tier 2/3) or inline (for smaller work):

- What was researched and why
- What options exist
- Which option you recommend and why
- What tradeoffs you considered
- What you ruled out and why

Do not dump raw research. Synthesize it into a recommendation. The research supports the recommendation — it is not the deliverable.

**When research is NOT needed:**

- You already know the stack and the right approach
- The user has already decided and just needs execution
- The problem is well-understood and the codebase already has patterns for it

Skip research and go straight to recommending.

### Step 4 — Recommend one path unless there are true alternatives

Prefer one strong recommendation over padded choices.

If there is one best path, recommend it and explain why. Present multiple options only when there are genuinely distinct viable paths with meaningful tradeoffs.

### Step 5 — Decompose small by default

If the work is bigger than one small coherent slice, decompose it inside brainstorming.

Default preference:

```text
Prefer more small coherent slices over fewer large slices.
```

Produce:

- Major sections/slices
- Recommended slice size and agent count
- Which slices can be parallel
- Which slices must run in series
- Interfaces between slices
- Shared files/data/contracts
- Owned files/pages/spec artifacts per slice
- Integration risk
- Whether skeleton/framework-first is required inside the plan
- Whether critic review should run automatically

### Step 6 — Composer guidance

For decomposed work, identify composer guidance:

```text
Recommended decomposition:
Recommended parallelism:
Series-only slices:
Shared interfaces:
Writable file/page ownership:
Integration risks:
Composer notes:
```

No two agents should modify the same file, page mock-up, CSV canvas, or implementation module in parallel. Shared-file work can still be decomposed, but those slices must run in series.

### Step 7 — Notebook update

If the work is Tier 2 or Tier 3, create or update a notebook entry:

```text
notebook/{YYYY-MM-DD}-{short-topic}.md
```

Record:

- Objective
- Expected outcome
- Context not captured elsewhere
- Brainstorm summary
- Decomposition summary
- Recommended next artifact

The notebook summarizes and links. Do not paste full plans/specs.

### Step 8 — Handoff

After the user accepts the direction:

- If product behavior/data/page/UI changes: hand off to `/write-spec`
- If the spec/canvases already exist and are current: hand off to `/write-plan`
- If this was only exploratory and no implementation follows: update notebook and stop

## What you do NOT do

- Do not write code.
- Do not write the implementation plan.
- Do not produce large prose specs.
- Do not create fake options.
- Do not promote reasoning to permanent rules.
- Do not pull broad vision context into implementation slices unless needed.
