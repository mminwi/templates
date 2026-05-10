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
- **AI maintains this codebase. No frameworks for human convenience.** Use frameworks/libraries only when they improve AI-maintainability or reliability more than they increase conceptual load. Libraries that encapsulate proven, debugged work are welcome. Frameworks that primarily organize code for human convenience, hide simple behavior, or force workaround code are rejected.
- **Structured specs are the review surface.** When behavior, data, or UI changes, update the CSV canvases, page mock-ups, interaction matrices, and state tables before planning implementation. The user should be able to review these artifacts instead of reading source code.
- **Planning engages notebook and tests.** If work is significant enough for a plan, it also gets a notebook entry and `tests/TEST_PLAN.md` update.
- **No fabricated content.** Never invent facts, file paths, function names, library APIs, or behaviors. If you don't know something, say so. Verify before claiming.

---

## Working Discipline

These rules operate on the AI during a session, not on the program. They exist because spec discipline can otherwise turn small changes into 40-minute silent exercises.

- **Tier the discipline by change size.** Match overhead to risk.
  - **Tier 0 — Tiny** (safe edit, no behavior change) — direct edit + quick verification.
  - **Tier 1 — Small** (local behavior/code change, low risk) — lean implementation + verification; update spec/test only if behavior changes.
  - **Tier 2 — Medium** (several files, one feature/page, meaningful behavior) — brainstorm if needed → spec canvases → plan/notebook/test plan → execute with reviewer.
  - **Tier 3 — Big** (multi-agent, multi-module, schema, architecture, high risk) — brainstorm with decomposition → spec canvases → composer → plan + critic loop + skeleton/framework + test plan → execute + separate reviewer.
  - State the classification briefly. The user should not need to know the tier labels.
- **Announce phases when a task will run long.** If you're about to work for more than ~5 minutes without checking in, say what phase you're in: *"Inventorying," "Editing," "Verifying."* The user shouldn't have to wait silently to find out whether you're stuck or done.
- **End every modifying turn with a change manifest.** One line per file touched: `path — what changed`. The user should be able to see what happened without running `git diff`. Skip the manifest only when no files were modified.
- **Spec-edit budget rule.** If updating the spec is taking longer than the underlying code change, the spec is too prescriptive for what the change requires. Trim it or stop — don't keep elaborating. Specs describe contract; they're not a place to relitigate every detail.
- **Skeleton/framework-first is part of planning.** When introducing meaningful structure, include skeleton/framework tasks in the plan. It is not a separate user-called workflow.
- **Separate-agent verification before done.** Non-trivial plans are not complete until a separate short-context reviewer verifies the work against the plan, specs, and test plan.
- **One strong recommendation.** Prefer one good recommendation over three padded options. Present multiple options only when there are genuinely distinct viable paths.
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
| Main workflow | `brainstorm` `write-spec` `write-plan` `execute-plan` |
| Folded into main workflow | `decompose` in `brainstorm`; `critic-review` and `skeleton-first` in `write-plan`; `verify` and `review-work` in `execute-plan` |
| Maintenance | `systematic-debug` `update-spec` `spec-drift-check` `audit-subsystem` `refactor` `backfill-tests` |

---

## Plan files

For Tier 2/3 changes, plan files live in `plans/{YYYY-MM-DD}-{short-task-name}.md`. They are committed to git so they form a decision log. Never delete an old plan — mark it superseded if the approach changes.

Any time a plan file is used, create or update a matching notebook entry in `notebook/{YYYY-MM-DD}-{short-topic}.md` and update `tests/TEST_PLAN.md`.

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
- The four-SOP workflow (`brainstorm` → `write-spec` → `write-plan` → `execute-plan`)
- Vocabulary primer (Gherkin, characterization tests, skeleton vs framework, etc.)
- Anti-patterns Hodos is designed to prevent
