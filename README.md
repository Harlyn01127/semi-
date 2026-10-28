# StudyPilot — WS-101 Full-Stack Development (Semi-Final)

AI-assisted study-task planner: **Next.js (App Router) + self-hosted
Supabase (Docker) + PostgreSQL with Row Level Security**, developed with
OpenCode and Matt Pocock's engineering skills.

![StudyPilot dashboard](docs/evidence/dashboard.png)

## Features

- Email/password **signup & sign-in** (session stored in HTTP-only-style SSR
  cookies; routes and APIs both guarded), with a dedicated `/signup` form
  (confirm-password + client checks).
- **Task CRUD**: create, list, inline **edit** (title/description/priority/
  due date), complete **toggle**, **delete** with confirmation — every
  mutation goes through a server API with validation; rows persist in Postgres.
- **Due dates**: optional `YYYY-MM-DD` deadline per task with calendar-aware
  validation, rendered as **Overdue / Due today / Due tomorrow / Due &lt;date&gt;`
  badges.
- **Filters & search**: status chips (all/open/done), priority select, and
  text search over the list — client-side, combinable, with a
  "Showing N of M" counter and a one-click **Clear filters**.
- **Validation** with field-level error messages (shared server module).
- **AI study planner** panel: turns open tasks into a plan via the local
  OpenCode CLI (`app/api/ai/route.ts`).
- **Defence in depth**: route-handler session checks + Postgres RLS policies.

## Prerequisites

| Tool | Notes |
| --- | --- |
| Docker Desktop | Runs the self-hosted Supabase stack |
| Node.js ≥ 20 (tested on 24) | `node --test` runs the TS tests natively |
| OpenCode CLI (optional) | Only for the AI planner panel (`opencode` on PATH) |

## Setup

### 1. Start the self-hosted Supabase stack

```powershell
cd C:\Users\User\Projects\supabase-project
docker compose up -d --wait     # or: sh run.sh start
docker compose ps               # all services should be (healthy)
```

The stack listens on **http://localhost:8000** (Envoy API gateway).
Data persists in `supabase-project/volumes/` — `docker compose down` never
deletes it.

### 2. Apply the application schema (idempotent)

```powershell
Get-Content supabase\schema.sql -Raw | docker exec -i supabase-db psql -U postgres -d postgres
```

### 3. Configure the app

`.env.local` (git-ignored) — create next to `package.json`:

```env
NEXT_PUBLIC_SUPABASE_URL=http://localhost:8000
NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY=<anon/publishable key from the stack .env>
```

> Never commit real keys. The publishable (anon) key is browser-safe because
> RLS bounds it; the service-role/secret key must never appear here or in
> client code.

### 4. Install & run

```powershell
npm.cmd install        # PowerShell blocks npm.ps1 — use npm.cmd
npm.cmd run dev        # http://localhost:3000
```

## Commands

| Command | What it does |
| --- | --- |
| `npm.cmd run dev` | Dev server with hot reload |
| `npm.cmd run build` | Production build (type-checks first) |
| `npm.cmd run start` | Serve the production build |
| `npm.cmd run typecheck` | `tsc --noEmit` |
| `npm.cmd run lint` | ESLint (Next.js flat config) |
| `npm.cmd test` | Unit tests: validation seam (fast, no server) |
| `npm.cmd run test:integration` | API CRUD tests — needs dev server **and** Supabase up |

## Using the app

1. Open http://localhost:3000 → redirected to `/login`.
2. **Create an account** via the `/signup` form (email, password, confirm
   password; the local stack auto-confirms emails) → `/dashboard`.
3. Add a task (try an empty title to see validation), edit it inline, tick it
   off, delete it. Refresh — the data is still there (Postgres).
4. Optional: *Generate plan* calls the OpenCode CLI with your open tasks
   (auto-located; `OPENCODE_BIN` overrides the path, `npm run ai:smoke`
   smoke-tests the route).
5. Sign out → `/dashboard` is redirected to `/login` again.

## Testing

```powershell
npm.cmd test                  # 16 unit tests
npm.cmd run test:integration  # 19 integration tests (server + database)
```

Real results, including failures encountered and fixed: **`docs/test-report.md`**.

## Documentation map

| File | Contents |
| --- | --- |
| `docs/requirements.md` | Guideline → implemented-feature matrix |
| `docs/architecture.md` | Next.js ⇄ Supabase ⇄ Docker request flow |
| `docs/implementation-plan.md` | Milestones with status |
| `docs/agent-workflow.md` | Real prompts, skills used, artifacts, debugging |
| `docs/database-schema.md` | Tables, constraints, RLS policies |
| `docs/erd.mmd` | Mermaid ERD |
| `docs/test-report.md` | Commands, results, failures, fixes |
| `docs/security-review.md` | Checks performed + remaining limitations |
| `docs/presentation-outline.md` | Slide-by-slide defense script |
| `docs/code-defense-guide.md` | Key files + 15+ Q&A for the oral defense |
| `docs/evidence/` | Sanitized screenshots & logs |
| `AGENTS.md`, `GLOSSARY.md`, `docs/adr/` | Agent instructions, domain vocabulary, ADRs |
| `.scratch/ws101-defense/` | Skill-produced spec + tickets |

## Repository layout

```
app/            App Router pages + API route handlers
components/     Client components (dashboard)
lib/            supabase clients, validation module
supabase/       schema.sql (idempotent DDL + RLS)
tests/          unit + integration suites (node:test)
docs/           defense deliverables
.opencode/skills/  Matt Pocock's skills, vendored from
                   https://github.com/mattpocock/skills @ 59db609
                   (provenance + byte-fidelity proof: .opencode/skills/SOURCE.md)
```

## Notes

- The Supabase stack lives **outside** this repo at
  `C:\Users\User\Projects\supabase-project` (its `.env` holds real secrets —
  keep it that way).
- Local-only demo configuration (email autoconfirm) is documented in
  `docs/security-review.md`; do not copy it to production.
