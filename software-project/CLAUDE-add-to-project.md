# CLAUDE-add-to-project.md

> **This is a template fragment, NOT a drop-in CLAUDE.md file.**
>
> **Do NOT copy this file into a project as-is and rename it.** It will override any existing CLAUDE.md in the target project and destroy rules that are already there.
>
> **Correct usage:**
> - **New project (no existing CLAUDE.md):** Copy the body of this file below the separator into a new `CLAUDE.md` at the project root. Fill in the "About this project" and "Project-specific rules" sections.
> - **Existing project (already has CLAUDE.md):** Merge only the sections that are missing — typically *Standing Rules*, *Working Discipline*, *Communication Style*, the menu pointer to `hodos.md`. Leave the existing project's rules intact.
>
> This file is named `CLAUDE-add-to-project.md` on purpose. Claude Code only auto-loads files named exactly `CLAUDE.md`, so this file will not accidentally be loaded if Claude Code is opened inside the template folder.

---

# CLAUDE.md — [PROJECT NAME]

> **CRITICAL RULE FOR ALL AI SESSIONS**
>
> Before touching any file in this project, read this file fully.
> Before modifying any module or feature, read the relevant spec.
> If the spec conflicts with the code, **STOP**. Do not silently fix either — ask the user.

---

## About this project

*(Fill in before first use. Delete sections that don't apply.)*

- **What this project is:** [one sentence — what the software does]
- **Primary users:** [who runs it]
- **Where it runs:** [server, cloud, local, embedded]
- **Language / stack:** [e.g., Python 3.12 + Flask + SQLite]
- **Key specs:** [link or path — usually `specs/`]
- **Vision docs:** [link or path — usually `vision/`]

---

## Standing Rules

These are the unbreakable rules. They apply to every action in this project.

- **Specs are load-bearing. Read [`specs/README.md`](specs/README.md) before touching anything in `specs/`.** Specs describe *how* the software is built — schema, routes, runtime behavior. The bar is "regenerate the program from these specs alone." Code that drifts from spec is a defect.
- **Specs describe how the software is built. Vision describes why it exists.** *If this content disappeared, would the code break?* Yes → spec. No → vision. Use cases, audience definitions, marketing language, philosophical rationale, market positioning, "this could also be used for X" — all go in `vision/`, never in `specs/`. When the user provides purpose-context while explaining a feature, capture the *feature* in the spec and the *context* in vision (or don't capture the context at all if it's just reasoning).
- **Don't promote reasoning to rules.** The user talks in long voice-dictated streams with strong context. Reasoning-out-loud is *not* policy. Distinguish what the user is *deciding* from what they're *reasoning about*. Reasoning is context for the immediate decision, not a permanent rule. Default to ephemeral. Only save to memory, CLAUDE.md, or specs when the user has explicitly stated a rule — not when they mentioned a constraint while working through a thought process. The test: *"Would the user, three weeks from now, be surprised to find this written down as a rule?"* If yes, don't write it down without an explicit *"make this a rule."*
- **AI maintains this codebase. No frameworks for human convenience.** Libraries that encapsulate proven, debugged work (crypto, parsing, perf-critical ops, external SDKs) are welcome. Frameworks that exist primarily to organize code for human readers (ORMs, state-management libraries, RPC layers, BDD test frameworks, dependency-injection containers, code generators) are rejected — they bulk up the program, reduce how much fits in a single context window, and obscure what the code is doing. Stay close to the core language.
- **No fabricated content.** Never invent facts, file paths, function names, library APIs, or behaviors. If you don't know something, say so. Verify before claiming.

---

## Working Discipline

These rules operate on the AI during a session, not on the program. They exist because spec discipline can otherwise turn small changes into 40-minute silent exercises.

- **Tier the discipline by change size.** Match overhead to risk.
  - **Small** (typo, prose tweak, single-file bugfix, copy change) — just code. Skip spec-first. Skip plan files. End with the change manifest below.
  - **Medium** (new field, new route, new component, refactor in one subsystem) — update the affected spec inline as part of the same edit. No separate planning ceremony.
  - **Big** (new subsystem, schema change, new external integration, multi-spec change) — design conversation first. Update specs across the affected surface in one pass *before* code.
  - When in doubt, ask which tier. Don't default to the heaviest.
- **Announce phases when a task will run long.** If you're about to work for more than ~5 minutes without checking in, say what phase you're in: *"Inventorying," "Editing," "Verifying."* The user shouldn't have to wait silently to find out whether you're stuck or done.
- **End every modifying turn with a change manifest.** One line per file touched: `path — what changed`. The user should be able to see what happened without running `git diff`. Skip the manifest only when no files were modified.
- **Spec-edit budget rule.** If updating the spec is taking longer than the underlying code change, the spec is too prescriptive for what the change requires. Trim it or stop — don't keep elaborating. Specs describe contract; they're not a place to relitigate every detail.
- **Skeleton-first for new subsystems.** When introducing a meaningful new module (new API surface, new background job, new admin page), write file paths + function signatures + empty bodies *first*. Show the scaffold before filling it in. Lets the user spot a missing piece or wrong shape before any logic exists.
- **Breadcrumb rule for `.claude/` modifications.** When modifying any file in `.claude/skills/` or `.claude/commands/`, log the rationale in two places: (1) a clear commit message — one line, what changed and why; (2) an entry in `.claude/CHANGELOG.md` for non-trivial changes — which skill, what behavior changed, why, what you'd test. Commit history + CHANGELOG together are the breadcrumb trail when reviewing the project's snapshot against the upstream template repo.

---

## Communication Style

- **Direct. No fluff.** Short responses preferred. The user reads quickly.
- **The user is a project manager and engineer, not a classically-trained programmer.** Explain technical decisions in plain terms. Avoid jargon unless you define it. The user ships software professionally; they understand the trade-offs once they're explained.
- **The user uses voice-to-text.** They talk in long streams, repeat themselves to reinforce points, and add context for thinking. Repetition is reinforcement, not ambiguity. Strip context-for-reasoning from the actual decision before acting on it.
- **Verify before claiming.** Don't say "done" or "fixed" without checking. Verification is an explicit step, not an assumption.
- **Remind the user to commit when meaningful work is done.** Check-ins are good habits, especially after a Big-tier change.

---

## Skill menu

The full skill list with trigger phrases is in [`hodos.md`](hodos.md). Speak naturally — describe intent — and the AI routes to the matching skill. Slash command names exist as overrides when auto-trigger guesses wrong.

Quick reference:

| Phase | Skills |
|---|---|
| 1 — Design (no code yet) | `brainstorm` `decompose` `write-spec` `flowchart` `write-plan` |
| 2 — Audit | `critic-review` |
| 3 — Build | `skeleton-first` `execute-plan` |
| 4 — Verify | `verification-before-completion` `review-work` |
| Maintenance | `systematic-debug` `update-spec` `spec-drift-check` `audit-subsystem` `refactor` `backfill-tests` |

---

## Plan files

For Big-tier changes, plan files live in `.claude/plans/{YYYY-MM-DD}-{short-task-name}.md`. They are committed to git so they form a decision log. Never delete an old plan — mark it superseded if the approach changes.

Small and Medium changes do not require plan files. The spec is the contract; for those tiers, updating the spec inline is enough.

---

## Project-specific rules

*(Fill in before first use. Remove or expand as needed. These are the rules that apply only to THIS project — not the generic Hodos rules above.)*

- **Code style:** [linter, formatter, conventions]
- **File path conventions:** [where things live]
- **Testing:** [how to run tests; what counts as "verified"]
- **Deployment:** [how code reaches production]
- **Do not touch:** [folders/files the AI must not modify]
- **Domain rules:** [unbreakable project-specific constraints — e.g., "13+ minimum age," "no PII in logs"]

---

## Hodos handbook

See [`hodos.md`](hodos.md) for:
- The full skill list with trigger phrases
- The four-phase mental model (Decompose → Audit → Build → Verify)
- Vocabulary primer (Gherkin, characterization tests, skeleton vs framework, etc.)
- Anti-patterns Hodos is designed to prevent
