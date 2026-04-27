---
description: Open work that involves creating or extending a feature — clarify what we're really trying to do, surface tradeoffs, propose an approach, get approval before producing any artifact
---

# /brainstorm

## When to use

Invoke at the **start** of work that involves:

- A new feature, component, module, or subsystem
- Extending or significantly changing existing behavior
- Any task where the user's first description is wide enough that you could reasonably build several different things from it

Trigger phrases (auto-fires when the user says):

- *"Let's brainstorm…"*
- *"What could we do about X?"*
- *"I'm thinking about X but I'm not sure…"*
- *"Help me figure out the approach for X"*
- *"How should we handle X?"*

Do **not** use for:

- Concrete tasks with bounded scope ("rename this function," "fix this bug") — go straight to the appropriate skill
- Maintenance work (refactoring, debugging, drift checks) — those have their own skills
- Anything Small-tier (just code it)

## Core discipline — design before artifact

This skill is the gate before `decompose` / `write-spec` / `write-plan`. Its only job is to converge on **what we're trying to do** and **the approach** with the user. No code, no spec file, no plan file is produced by this skill — that's the next skill's job.

The hard rule: **do not invoke any artifact-producing skill until the user has approved the approach.** This applies to every project regardless of perceived simplicity. Simple projects are where unexamined assumptions cause the most wasted work.

## Process

### Step 1 — Read the current project context

Before asking anything, look at:

- `CLAUDE.md` and the project's standing rules
- `specs/` — what already exists
- Recent git log — what's been changing
- Any obvious files related to the user's request

This takes 1-2 minutes. It prevents asking questions whose answers are already in the project.

### Step 2 — Assess scope before asking detail questions

If the request describes **multiple independent subsystems** (e.g., "build a platform with chat, file storage, billing, and analytics"), flag this immediately. Don't spend questions refining details of a project that needs to be decomposed first. Hand off to `/decompose`.

If the request is appropriately scoped, proceed.

### Step 3 — Ask clarifying questions, one at a time

- **One question per message.** If multiple questions are needed, ask them in sequence, not as a list.
- **Prefer multiple-choice when possible.** "Should this be A, B, or C?" is faster to answer than "What do you want?"
- **Focus on:** purpose, constraints, success criteria, hard limits.
- **Don't ask about implementation details** the user wouldn't reasonably know yet. Save those for the spec phase.

### Step 4 — Propose 2-3 approaches with tradeoffs

Once you have enough to recommend, present 2-3 candidate approaches. For each:

- **Approach name** (one phrase)
- **What it looks like** (1-2 sentences)
- **Tradeoffs** (what you give up)
- **Recommended fit** (which kind of project/use case it suits best)

End with **your recommendation** and the reason. Don't be neutral — pick one and say why.

### Step 5 — Present the design in sections, get approval

Present the chosen approach in sections sized to be readable in one screen each. After each section:

- Ask the user to approve, push back, or refine.
- Do not move to the next section until the current one is approved.

### Step 6 — Capture and hand off

Once the design is approved:

- **Brief design summary** (one paragraph) goes into the user's record of the conversation. No formal design doc unless the work is Big-tier.
- Hand off to `/decompose` (if the design has multiple slices), `/write-spec` (if a single feature spec is the next artifact), or `/write-plan` (if the design is small enough to plan directly).

For Big-tier work, the brainstorm output itself can be saved to `plans/{YYYY-MM-DD}-{topic}-brainstorm.md` so the design conversation isn't lost.

## Rules

1. **No code, no spec, no plan.** This skill produces a design conversation only.
2. **No "this is too simple to need design."** Every project gets brainstorming. The conversation can be short for simple work, but it must happen.
3. **Don't promote reasoning to rules.** When the user works through tradeoffs, the reasoning is context for *this* decision, not a permanent rule. See CLAUDE.md `Don't promote reasoning to rules`.
4. **One question at a time.** Don't list five questions and ask the user to answer them all.
5. **Recommend, don't survey.** When proposing approaches, pick a favorite and say why.
6. **Approval gates between sections.** Long designs are presented in chunks; each chunk is approved before the next.

## What you do NOT do in this skill

- Do not write code
- Do not create spec files (that's `/write-spec`)
- Do not create plan files (that's `/write-plan`)
- Do not enumerate every implementation detail — that's the spec's job
- Do not move past a section the user hasn't approved
