# StudyPilot — agent instructions

StudyPilot is a study-task planner: Next.js App Router app, self-hosted Supabase (Docker) backend, PostgreSQL with RLS. Work in this repo builds and defends it for WS-101.

## Agent skills

### Issue tracker

Local markdown issues in `.scratch/<feature>/`. See `docs/agents/issue-tracker.md`.

### Domain docs

Single-context: read `GLOSSARY.md` and `docs/adr/` before touching the area they cover. See `docs/agents/domain.md`.

## Working agreement

1. **Verify red before you hypothesise.** Reproduce with a command (`npm run test`, `node --test tests/api-crud.test.ts`, `curl` against `localhost:3000`) before changing code. The `diagnosing-bugs` skill owns this loop.
2. **Test-first at agreed seams.** Seams: `lib/validation.ts` (pure), `/api/tasks` HTTP routes (integration), browser UI (manual). Write the failing test, then the minimal code. The `tdd` skill owns the loop.
3. **Never write secrets into source, docs, or screenshots.** `.env.local` is git-ignored; service-role/supabase-secret keys stay server-side only.
4. **Data safety:** the Supabase stack in `C:\Users\User\Projects\supabase-project` keeps real data in `volumes/`. No `docker compose down -v`, no resets, no dropping tables.

## Environment gotchas (not discoverable from config)

- Windows PowerShell: call `npm.cmd`/`npx.cmd` (execution policy blocks `npm.ps1`). Node lives at `%LOCALAPPDATA%\Programs\nodejs\node-v24.21.0-win-x64` (per-user install; already on user PATH).
- Integration tests need two servers up: Supabase on `http://localhost:8000` (`docker compose up -d --wait` in the supabase-project dir) and the app on `http://localhost:3000` (`npm run dev`).
- The AI planner route shells out to the OpenCode CLI. It auto-resolves the binary (`OPENCODE_BIN` env var → per-user desktop install `resources\opencode-cli.exe` → PATH); no PATH setup needed. The CLI is spawned `--standalone` with file-descriptor stdio because the Bun-built binary hangs on piped stdio from Node (`npm run ai:smoke` verifies).

## Where things live

- Defense deliverables (README, requirements, architecture, test report, security, Q&A): `docs/` — start at `docs/agent-workflow.md` for the build story.
- Spec/tickets produced by the `to-spec` / `to-tickets` skills: `.scratch/`.

<!-- BEGIN:nextjs-agent-rules -->

## This is NOT the Next.js you know

This version has breaking changes — APIs, conventions, and file structure may all differ from your training data. Read the relevant guide in `node_modules/next/dist/docs/` (resolved from this file's directory; in monorepos the `next` package may not be visible from the repo root) before writing any code. Heed deprecation notices.

This block is written and re-added by `next dev` — verify at `node_modules/next/dist/server/lib/generate-agent-files.js`. Removing it from a diff only re-creates the uncommitted change; committing it with your work keeps the tree clean.

<!-- END:nextjs-agent-rules -->
