# Tooling & Implementation Philosophy

> Standing rules for technology choices in an AI-maintained software project. The summary version of this rule lives in [`../CLAUDE-add-to-project.md`](../CLAUDE-add-to-project.md) under *Standing Rules*. This file is the operational depth — the evaluation checklist, decision rules, and rationale you use when actually deciding whether to add a dependency.
>
> Carry this file forward into your project. Pair it with a project-specific stack inventory (see "How to use this in your project" at the end).

---

## Core principle

**Rule.** This codebase is written and maintained by AI. No human-in-the-loop is intended for ongoing maintenance. Every tooling, library, and framework choice must serve that reality — not the legacy goal of helping humans navigate a codebase.

**Rationale.** Conventional software architecture optimizes for human comprehension at scale: layers of abstraction, opinionated frameworks, dependency-injection containers, state-management libraries, ORM mapping layers. Each of these adds complexity that an AI maintainer doesn't need (or actively struggles with). They bulk up the program, make less of it fit into a single context window, increase the surface area for bugs that look right at one layer but break at another, and obscure what the code is actually doing. None of it pays for itself when no human is reading the code.

**Consequence.** Stay as close to the core programming language as practical. Let the AI see what the code is doing directly. Reach for libraries when they encapsulate proven debugged routines (often perf-critical, sometimes written in faster languages, tested at a scale we can't replicate). Avoid frameworks that exist primarily to organize code for human readers.

---

## Library vs framework

### Library

A library exposes functions/types you call. You stay in control of structure and flow. Examples: a Postgres driver, a hashing library, a math-rendering library, an external-service SDK.

**Verdict.** OK — add when needed. Especially OK when the library encapsulates work that's hard to get right, perf-sensitive, or lives in a faster runtime (C, Rust).

### Framework

A framework dictates structure, flow, and lifecycle. It calls your code; you fit into its model. Examples of what we DO NOT want: state-management libraries (Redux, MobX, RxJS, Zustand, Recoil), opinionated server frameworks layered on top of the runtime, ORMs of any kind, typed-RPC layers, BDD test frameworks, dependency-injection containers, code generators that produce boilerplate scaffolding.

**Verdict.** Default to NO. Frameworks add layers of indirection that primarily benefit human readers — *"this is the X layer, this is the Y layer."* AI doesn't need that scaffolding to navigate code. Each framework reduces how much of the program fits into a single context window and increases the chance of bugs that span its abstraction boundary.

---

## Evaluation checklist

Walk through these questions before adding any dependency:

1. Does this dependency exist primarily to help a human conceptualize the codebase? → **Reject.**
2. Does it encapsulate proven, hard-to-reimplement work (cryptography, parsing, perf-critical ops, integration with external services)? → **Likely accept.**
3. Does it dictate structure (where files go, how flow runs, what shape your code takes)? → **Strong push to reject.**
4. Can a competent AI session reimplement the same functionality in <200 lines of plain code? → **Reimplement instead of importing.**
5. Does it reduce or increase the amount of code I need to read to understand any single feature? → If it increases (more files, more abstraction layers), **reject.**
6. Does it add a layer between us and the underlying primitive (DB, HTTP, filesystem, AI API)? → **Push to reject.**

---

## Decision rules

### Adding a new dependency

1. Walk through the evaluation checklist above.
2. If it passes, add it.
3. If it fails, write a short note in the relevant spec explaining why we considered it and why it didn't pass. Future-you will thank present-you for the citation.
4. Update the project-specific tooling spec's approved or rejected list accordingly. The lists must reflect current reality.

### Encountering existing framework use

1. If a feature relies on a framework not on the approved list, flag it.
2. Don't reflexively rip it out — some frameworks are grandfathered for foundational reasons.
3. Discuss with the user before removing a foundational framework. Reimplement smaller-scope frameworks as plain code when you touch them.

### Writing new code

- Default to plain functions and modules. Compose by importing.
- Default to raw SQL via the database driver. Never reach for an ORM.
- Default to local component state. Reach for context only when the same data is needed by widely separated components.
- Default to plain HTTP + JSON for client-server communication. Don't introduce typed-RPC layers.
- When in doubt, write the simplest version first. Refactor only when concrete need appears — not in anticipation.

---

## Voice quotes

The user's framing of these principles, in their own words. Keep these verbatim — they carry weight as a tooling-decision argument and shouldn't be paraphrased.

> *"The entire project will likely be maintained by AI or written and maintained by AI. There's not really a human in this process; there isn't intended to be one. With libraries and frameworks, I don't want any libraries and frameworks put into the project that are predominantly there to help facilitate a human from understanding or conceptualizing things."*

> *"I don't want frameworks put in because they add a layer of complexity that the AI doesn't need, and they just bulk up the program. They make it harder for the AI to be able to pull more of it into context or into its memory at a time, and they make it harder to troubleshoot."*

> *"Libraries will be needed, obviously, where you've got proven routines that have been debugged; they work. They may be written in other languages so they're faster, so there are reasons to have libraries and stuff."*

---

## How to use this in your project

This file holds the universal philosophy. Pair it with a project-specific tooling spec (often a JSON file like `<project> Tooling.json`) that records:

- **Approved stack** — the language, runtime, and any frameworks grandfathered as foundational.
- **Approved libraries** — specific packages and versions, with one-sentence rationale per entry.
- **Rejected** — packages considered and not adopted, with a short reason. Captures the *why* so it isn't relitigated.

When evaluating a new dependency, the project-specific spec is what gets updated. This file rarely changes — it's the principle, not the inventory.
