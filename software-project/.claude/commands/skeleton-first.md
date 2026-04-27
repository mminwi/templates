---
description: Write the structure of the code first — module shape, class/function signatures, docstrings, TODO markers — and wait for user approval before implementing. Also known as "write the framework" or "do the framework first"
---

# /skeleton-first

> **Terminology note:** In mechanical-engineering parlance, the user calls this the **framework** — the structural backbone before detail features are added. "Skeleton" and "framework" are interchangeable here. When the user says "do the framework first," "write the framework," or "frame it up," invoke this skill. When speaking back to the user, prefer "framework" over "skeleton" in explanations.

## When to use

Invoke at the start of Phase 3, after the design artifacts are approved, the critic has passed the plan, and it's time to write code — but before any implementation exists.

Trigger phrases: "skeleton first," "write the skeleton," **"framework first," "write the framework," "frame it up," "do the framework"**, "write the structure only."

This skill produces the **shape** of the code, not the logic. A reviewer (the user, or another AI) can look at the framework and catch design problems before any real implementation is written. Design mistakes are cheap to fix at this stage and expensive to fix after.

## The analogy (for a PM or CAD user)

- **Mechanical framework:** the chassis, frame, or structural backbone — built first, then skin/fixtures/components attach to it. Same idea here.
- **CAD:** block layout before detailed features. You verify the overall shape is right before spending time on fillets and chamfers.
- **Mechanical design:** envelope dimensions before internal geometry. You confirm it fits in the space before designing the mechanism inside.
- **PLC:** declare all I/O tags and subroutine names before writing rung logic.

The code framework serves the same purpose. You review the shape. You fix shape problems now. Then you fill in.

## What you must produce

Real code files with structure but no logic. Every file named in the plan's "Files touched" table gets a skeleton entry.

### For new modules / classes

```python
"""
Module: bd_verified_customer
Purpose: Marks and tracks companies tagged as verified customers.
Called by: the company detail page, the audit log reader.
"""

from typing import List, Optional


class VerifiedCustomerService:
    """Service for tagging and querying verified customer status."""

    def __init__(self, db_connection):
        """Initializes with a database connection."""
        # TODO: store connection
        pass

    def mark_verified(self, company_id: str, actor_user_id: str) -> bool:
        """Tags a company as a verified customer.

        Returns True on success, False if already tagged (idempotent).
        Writes an entry to the audit log.
        Raises PermissionError if actor is not admin.
        """
        # TODO: implement — see plan task #3
        pass

    def is_verified(self, company_id: str) -> bool:
        """Returns True if the company is tagged as verified."""
        # TODO: implement — see plan task #4
        pass

    def list_verified(self) -> List[str]:
        """Returns list of company IDs that are verified."""
        # TODO: implement — see plan task #5
        pass
```

### For new routes / handlers

```python
@app.route('/companies/<company_id>/verify', methods=['POST'])
def verify_company(company_id: str):
    """POST — marks a company as verified customer.

    Preconditions: user is admin (checked via @require_admin).
    Gherkin: spec/events/mark-company-verified.md — scenarios Happy/Non-admin/Already-verified.
    Side effects: audit log write, page refresh on next load.
    """
    # TODO: implement — see plan task #6
    pass
```

### For new data / schema

```sql
-- Migration 031: verified_customer tag
-- Related plan: plans/2026-04-24-verified-customer-tag.md
-- Related spec: spec/events/mark-company-verified.md

-- TODO: add `is_verified_customer` column to bd_companies (BOOLEAN, default 0)
-- TODO: backfill existing rows to 0
-- TODO: add index if query patterns warrant (see plan task #2)
```

### For modifying existing code

Read the existing file first. Produce a diff-style proposal:

```
File: aims/web/bd/models/company.py

Current structure:
- class Company
  - __init__
  - save()
  - to_dict()

Proposed changes:
- class Company
  - __init__               (unchanged)
  - save()                 (unchanged)
  - to_dict()              (modify — include new `is_verified_customer` field)
  - mark_verified()        (NEW — see plan task #7)
  - is_verified()          (NEW — see plan task #8)
```

## Rules

1. **Signatures must be complete.** Parameter names, types (where the language supports them), return types, and docstrings. A reviewer must be able to read the skeleton and understand what each function will do.

2. **Docstrings describe behavior, not implementation.** Good: "Returns True on success, False if already tagged (idempotent)." Bad: "Uses SQLAlchemy to INSERT into the tags table."

3. **Every function body is `pass` or `# TODO: ...`.** No partial implementations. Either empty or a stub. No sneaking logic in.

4. **Link back to the plan.** Every TODO references the plan task number. This is how `/execute-plan` resumes.

5. **No invented scope.** If the plan says create three functions, create three functions. Don't add a fourth "while I'm in here."

6. **Tests get skeletons too.** For every Gherkin scenario, a test stub exists (even if the test body is `pass`). This catches missing tests at the shape stage.

## Workflow

1. Read the approved plan.
2. For each file in the "Files touched" table, produce the skeleton (or diff proposal for existing files).
3. **Stop.** Do not proceed to implementation.
4. Present the skeleton to the user.
5. The user reviews the shape. Catches any design mistakes now.
6. Iterate on the skeleton if needed.
7. Only when the user explicitly approves the skeleton do you invoke `/execute-plan`.

## Output handoff

- Skeleton files committed (or staged — user decides)
- Plan file updated: skeleton tasks marked complete, implementation tasks still open
- User invited to review before `/execute-plan` begins

## What you do NOT do in this skill

- Do not write any function body beyond `pass` or a TODO comment
- Do not create files not named in the plan
- Do not invent new functions that the plan didn't specify
- Do not skip tests — skeleton includes test stubs
- Do not proceed to implementation without explicit user approval of the skeleton
