# {{Project name}} — Operations Runbook

Solo-dev runbook. Every recipe is self-contained — copy-paste a command and it works. When something breaks at 11pm, this is the first place to look.

**Suggested sections:**
- Local development (start / stop the dev server, reset build cache)
- Database (apply schema, seed, reset, regenerate snapshots, inspect)
- Tests (e2e, unit, single-file runs)
- Background tasks (cron jobs, scheduled scripts)
- Third-party services (one bullet per: what it does, env var it uses, console URL, what breaks if down)
- Common breakages (real recipes for things that have actually broken — keep this growing)
- Aspirational (TODO) — things you'll need eventually (backups, key rotation, deploy)

---

<!-- TODO: fill in real recipes as the project grows. Sample shape:

## Local development

### Start the dev server
```bash
npm run dev
```
Server at http://localhost:3000.

### Kill a stuck dev server
```bash
pkill -f "next dev" 2>/dev/null
lsof -ti:3000 | xargs -r kill -9
```

-->
