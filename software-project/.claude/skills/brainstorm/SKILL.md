---
name: brainstorm
description: "Use this BEFORE any creative or feature work — creating features, building components, adding functionality, modifying behavior, or any task where the first description is wide enough to build several different things from. Open the work, clarify what we're really trying to do, surface tradeoffs, propose 2-3 approaches, get user approval before any artifact is produced."
---

# brainstorm

Open the work that involves creating or extending a feature. No code, no spec file, no plan file is produced — that's the next skill's job. The single goal is to converge on **what we're trying to do** and **the approach** with the user.

## When this skill fires

Auto-fires on:

- *"Let's brainstorm…"*
- *"What could we do about X?"*
- *"I'm thinking about X but I'm not sure…"*
- *"Help me figure out the approach for X"*
- *"How should we handle X?"*

Plus: any wide opening of new feature work — even if the user didn't use a brainstorm trigger phrase.

## What this skill does

- Reads project context (`CLAUDE.md`, `specs/`, recent git log) before asking any question
- Asks clarifying questions one at a time, never as a list
- Proposes 2-3 candidate approaches with tradeoffs and a recommendation (not neutral)
- Presents the design in screen-sized sections, getting user approval after each
- Hands off to `decompose`, `write-spec`, or `write-plan` once design is approved
- For Big-tier work, captures a brainstorm summary at `plans/{YYYY-MM-DD}-{topic}-brainstorm.md`

## Hard rule

Do NOT invoke any artifact-producing skill (write code, create spec file, create plan file) until the user has approved the approach. This applies to every project regardless of perceived simplicity. "Simple" projects are where unexamined assumptions cause the most wasted work.

## Full instructions

Complete behavior contract — when to use, when NOT to use, full process, rules, anti-patterns — in [`../../commands/brainstorm.md`](../../commands/brainstorm.md).

## Related skills

- After approval, hand off to: `decompose` (if multiple slices), `write-spec` (if a feature spec is the next artifact), `write-plan` (if work is small enough to plan directly)
- Closely related to: `write-spec` (don't run brainstorm if a clear spec already exists)
