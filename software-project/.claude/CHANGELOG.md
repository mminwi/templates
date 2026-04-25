# `.claude/` Changelog

Per the breadcrumb rule (see `CLAUDE-add-to-project.md` Working Discipline): when modifying any file in `.claude/skills/` or `.claude/commands/`, log non-trivial changes here. Trivial changes (typo fixes, formatting) only need a commit message.

## Format per entry

```
## YYYY-MM-DD — <short title>

**Files touched:** path/to/file (and any others)
**What changed:** one to three sentences describing the behavior change.
**Why:** the reason — what wasn't working, what motivated this, what test scenario surfaced it.
**Test:** how you'd verify the new behavior is correct (or note "not yet tested in real session" if the change hasn't been exercised).
**Backport status:** whether this change came from the dogfood project (e.g., PNT) or originated here.
```

Most-recent entries at the top. Older entries below.

---

## 2026-04-25 — Initial template state

**Files touched:** `.claude/commands/` (16 skills) + `.claude/skills/` (16 SKILL.md auto-trigger files), plus `README.md`, `CLAUDE-add-to-project.md`, `hodos.md`, `specs/README.md`, `vision/README.md`.
**What changed:** First committed version of the evolved Hodos template. Replaces the earlier 17-skill version with a leaner 16-skill set; adds `.claude/skills/` for auto-trigger; introduces the two-path docs structure (`specs/` + `vision/`); adds Working Discipline rules (tier by change size, change manifest, phase markers, spec-edit budget, skeleton-first); adds the don't-promote-reasoning-to-rules firewall.
**Why:** Earlier Hodos was 17 slash commands and a thin handbook. After dogfooding through PNT Learning's spec work, the gaps surfaced: scope clarification, ambiguity vs wordiness, Vision-vs-spec contamination, the "40-minute silent stretch" failure mode. This version closes those gaps.
**Test:** Apply to a new software project. Verify auto-trigger fires on natural language for each skill. Verify the cheat sheet `hodos.md` is readable and answers "how do I say X?"
**Backport status:** Originated here through design conversation; informed by PNT Learning observations.
