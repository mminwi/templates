# {{Project name}} — Environment Variables

Every env var the app reads, what it does, and how to obtain it. Future-self provisioning a fresh environment should walk down this doc and finish in ~30 minutes.

**Do NOT include actual secret values in this file.** Document names, purposes, sources, and fallbacks only.

**Field reference:**
- **Purpose** — what the var controls
- **Required** — Yes (app won't start) / Conditional (feature won't work) / No (dev-only or has fallback)
- **Where to get** — service console URL or instruction to generate
- **Used by** — files / routes / scripts that read it
- **Fallback** — what happens if missing
- **Notes** — naming gotchas, rotation cadence, server-side vs. client-side (`NEXT_PUBLIC_*` etc.)

---

<!-- TODO: replace this skeleton with one block per env var. Group by purpose (Database, Auth, AI providers, Payments, Email, Observability, etc.).

## Database

### DATABASE_URL
- **Purpose:** Postgres connection string.
- **Required:** Yes — app cannot start without it.
- **Where to get:** Local `postgres://USER:PASS@localhost:5432/DBNAME` or your managed host.
- **Used by:** src/lib/db.ts, every tsx scripts/*.ts script.
- **Fallback:** None — fail fast.
- **Notes:** —

-->
