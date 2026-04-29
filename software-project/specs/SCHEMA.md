# {{Project name}} — Data Model

Per-table inventory. One block per table. Group by domain (Auth & Identity, Curriculum, Sessions, Billing, etc. — pick what fits the project). Source of truth for the actual DDL is whatever your project uses (`schema.sql`, `prisma/schema.prisma`, migration files); this doc is the human-readable companion.

**Field reference:**
- **Purpose** — one-line reason the table exists
- **Fields** — column name (TYPE) — short description; mark NOT NULL with `*`, FK references with `→ Other.field`
- **Indexes** — non-PK indexes; one line each
- **Writers** — which routes / scripts INSERT or UPDATE this table
- **Notes** — invariants, retention rules, gotchas, history

---

<!-- TODO: replace this skeleton with one block per table.

## Domain — Auth & Identity

### TableName
- **Purpose:** ...
- **Fields:**
    - id (TEXT PK) — CUID
    - field2 (TEXT*) — required
    - field3 (TEXT) → OtherTable.id
    - createdAt (TIMESTAMPTZ*)
    - updatedAt (TIMESTAMPTZ*)
- **Indexes:** idx_foo on (...)
- **Writers:** /api/auth/signup, scripts/seed.ts
- **Notes:** ...

-->
