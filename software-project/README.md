# Hodos — Software Project Template

A starter template for AI-assisted software projects. Loads a working discipline, a Claude skill set, and a documentation structure (`specs/` + `vision/` + `plans/` + `notebook/` + `tests/`) into a fresh project so you don't rebuild the rails every time.

*Hodos* (Greek: ὁδός — "way," "path"; the root of *method*, *exodus*, *synod*) names the process so it can be referenced quickly: *"Mike's running Hodos on this," "did the critic review pass?"*

This template is for **software projects** — code, schema, deployments. Other project types (data analysis, technical writing, project management) need their own templates if they're going to use Hodos discipline; this one stays software-focused on purpose.

---

## What's in here

```
hodos_template/
  README.md                     ← this file
  CLAUDE-add-to-project.md      ← the CLAUDE.md fragment to merge into your project
  hodos.md                      ← the cheat sheet you open while talking
  specs/                        ← seed for project's specs/ — READMEs only
    README.md                   ← what goes in specs/, what doesn't
  vision/                       ← seed for project's vision/ — READMEs only
    README.md                   ← what goes in vision/, what doesn't
  notebook/                     ← engineering notebook entries
    README.md                   ← trace log convention
  tests/
    TEST_PLAN.md                ← cumulative test plan
  .claude/
    commands/                   ← slash-command overrides for the skill set
    skills/                     ← (next pass) auto-trigger SKILL.md files
```

---

## Core ideas

### Two paths for documentation

`specs/` and `vision/` are different folders with different rules:

- **`specs/`** — describes how the software is built. Schema, routes, runtime behavior, data shapes. *If this content disappeared, the code would break.*
- **`vision/`** — describes why the software exists, who it's for, what it means. Marketing copy, audience definitions, philosophical rationale, market positioning. *If this content disappeared, the code would still work — it would just lose meaning.*

The most common contamination: vision content leaks into specs because the user explained *why* a feature exists while specifying *how* it should work. Hodos's discipline keeps the two separate. See `specs/README.md` and `vision/README.md` for the rules.

### Working Discipline (operates on the AI, not the user)

Core rules in `CLAUDE.md` shape how the AI works during a session:

1. **Tier the work to the change size** — tiny work stays light; medium/big work uses specs, plans, notebook, tests, and review.
2. **Announce phases when running long** — "Inventorying," "Editing," "Verifying." No 40-minute silent stretches.
3. **End every modifying turn with a change manifest** — `path — what changed`. The user shouldn't have to `git diff` to see the result.
4. **Structured specs first for meaningful behavior changes** — CSV canvases, mock-ups, interaction matrices, and state tables before implementation planning.
5. **Planning engages notebook and tests** — any plan also links a notebook entry and updates `tests/TEST_PLAN.md`.
6. **Separate-agent verification before done** — non-trivial work is not done until a fresh reviewer verifies plan/spec/test alignment.

### Don't promote reasoning to rules

The user talks in long voice-dictated streams with strong context. Reasoning-out-loud is *not* policy. The AI extracts the actual decision and confirms before acting; it does not silently save constraints mentioned during reasoning into CLAUDE.md, specs, or memory. See the firewall rule in `CLAUDE-add-to-project.md`.

### Auto-trigger skills (slash overrides available)

The skill set is invoked by natural language. Speak naturally — describe intent — and the AI routes to the matching skill. Slash command names exist as explicit overrides for when auto-trigger guesses wrong. See `hodos.md` for the full skill index with trigger phrases.

---

## How to seed a new project

```bash
# clone the template into a working directory
git clone <hodos-template-repo-url> my-new-project
cd my-new-project

# detach from the template's git history; start fresh
rm -rf .git
git init

# rename the CLAUDE fragment into the actual loaded file
mv CLAUDE-add-to-project.md CLAUDE.md

# fill in the placeholders in CLAUDE.md (project name, language, stack)

# initial commit
git add -A
git commit -m "Seed project from Hodos template"
```

The template's `specs/` and `vision/` folders carry only their READMEs at first. As the project grows, real spec and vision content fills in.

### Updating the template later

This template is a **snapshot at project-start**. Future improvements to the template do not auto-propagate to projects already seeded from it. If a year later you want to pull a template improvement into an existing project, do it as a manual diff:

```bash
# in your project, see what's new in the template
diff -r ~/templates/hodos/ .

# bring in the parts you want, leave your project-specific edits alone
```

That's the right tradeoff — old projects don't break when the template evolves, and you keep the freedom to evolve it.

---

## Adapting the template to an existing project

If a project already has a `CLAUDE.md`:

1. Copy `hodos.md`, `specs/README.md`, and `vision/README.md` into the project.
2. **Merge** sections from `CLAUDE-add-to-project.md` into the existing `CLAUDE.md`. Typical sections to add: *Standing Rules*, *Working Discipline*, *Communication Style*, the menu-pointer to `hodos.md`. Leave the project's existing rules alone.
3. Copy `.claude/commands/` into the project's `.claude/commands/` (merge by filename — skip any the project already has).

---

## Skills at a glance

Full list with trigger phrases is in [`hodos.md`](hodos.md). Quick reference:

| Area | Skills |
|---|---|
| Main workflow | `brainstorm` `write-spec` `write-plan` `execute-plan` |
| Folded into main workflow | `decompose` in `brainstorm`; `critic-review` and `skeleton-first` in `write-plan`; `verify` and `review-work` in `execute-plan` |
| Maintenance | `systematic-debug` `update-spec` `spec-drift-check` `audit-subsystem` `refactor` `backfill-tests` |

---

## Evolving the template

Use this template for a few projects. When a pattern is missing, add it. When a skill proves unnecessary, remove it. The template is yours — edit freely.

If a skill improvement is genuinely generic (applies to every software project), update this template. If it's project-specific, keep it in that project's `.claude/commands/` only.

## Active dogfood project

**PNT Learning** is the active dogfood project for this template. The version of `.claude/skills/` and `.claude/commands/` inside the PNT repo is a working snapshot — improvements made there during real PNT work get periodically reviewed and merged back here. If two versions disagree, the PNT-side copy is usually more recent (it's where new patterns get tested first).

Periodic-merge workflow:

1. While working on PNT, modifications to `.claude/skills/` or `.claude/commands/` get logged per the breadcrumb rule (commit messages + `.claude/CHANGELOG.md`).
2. Periodically (e.g., end of a sprint, or when starting a new project), review the PNT-side CHANGELOG vs this template's git log.
3. Decide per change: merge into the template (universal improvement) or leave PNT-only (project-specific tweak).
4. Apply chosen changes here as a clean commit; update this template's CHANGELOG with the merge entry.

The template repo lives in Git at the parent `templates/` folder — commits in that repo are the long-term breadcrumb trail.
