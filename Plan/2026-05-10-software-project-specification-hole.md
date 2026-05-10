# Plan: Tighten Hodos Specification Convergence

## Status

Superseded by `2026-05-10-software-project-specification-convergence-v3.md`. This file is incomplete and retained only as a historical scratch artifact.

## Context

The `software-project/` template currently provides a strong Hodos workflow for AI-assisted software work. It is heavily influenced by the Superpowers-style skill workflow and currently emphasizes:

- Brainstorming
- Planning
- Critic review
- Execution
- Verification
- Spec drift checks
- Skeleton/framework-first implementation
- Separation of `specs/` and `vision/`

This is useful, but the current weak point appears to be the **specification step**.

The issue is not only that specs need to be “better written.” The deeper issue is that loose narrative intent is being converted into plans too quickly. Planning is being asked to perform too many jobs at once:

- Extract requirements
- Resolve ambiguity
- Detect contradiction
- Define behavior
- Decompose work
- Sequence implementation
- Set acceptance criteria
- Bound agent scope

That makes the plan either too broad or too vague. When execution begins, the coding agent still has to make product/design decisions. On small programs this works. On larger programs it causes drift.

## Core Problem

Current rough flow:

```text
brainseed / brainstorm
  → plan
  → critic
  → execute