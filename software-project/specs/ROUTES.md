# {{Project name}} — API & Server Actions

Inventory of every HTTP route handler and every server action. Group by area (`/api/auth`, `/api/admin`, etc.). One block per HTTP method per route.

**Field reference:**
- **Purpose** — one-line reason the endpoint exists
- **Audience** — role(s) the route accepts (per `requireRole(...)` or equivalent gate); `Public` if no auth
- **Body** — request body shape (key fields only); `—` if GET
- **Response** — response shape (key fields); status code only if trivial
- **Audit logged** — yes / no
- **Notes** — rate limiting, transaction handling, edge cases

---

<!-- TODO: replace this skeleton with one block per (METHOD, path).

## /api/auth

### POST /api/auth/login
- **Purpose:** Sign in.
- **Audience:** Public
- **Body:** `{ identifier: string, password: string }`
- **Response:** `{ ok: true, role: string }` on success; 401 on bad credentials
- **Audit logged:** yes
- **Notes:** Rate-limited at 5 attempts per IP per 15 min.

-->

## Server actions

<!-- TODO: list each `"use server"` function. Format:

### File / function
- **File:** src/app/.../page.tsx
- **Function:** transitionStatus
- **Audience:** ADMIN
- **Purpose:** ...
- **Form data:** id, status
- **Side effects:** updates `Foo.status`, revalidates `/admin/foo`
- **Audit logged:** yes
-->
