# Glossary

Shared vocabulary for StudyPilot (single-context repo). Terms are what tests,
specs, and commits should use.

| Term | Meaning |
| --- | --- |
| **Task** | The core domain record: a study item with `title`, optional `description`, `priority` (low/medium/high), `completed` flag, owned by one user. |
| **Owner** | The `auth.users` row a Task belongs to (`user_id`). Only the owner sees/edits their tasks. |
| **StudyPilot account** | An `auth.users` identity created through signup; carries the session that the app's SSR cookies encode. |
| **Session cookie** | The `sb-*` cookie(s) written by `@supabase/ssr`; the only credential the browser ever holds. |
| **Publishable key** | The anon/public API key safe for browsers; its powers are bounded by RLS. |
| **Secret key** | The service-role API key. Server-only, never shipped to the browser. |
| **Seam** | A public boundary where tests observe behavior: `lib/validation.ts`, the `/api/tasks` HTTP surface, the browser UI. |
| **RLS** | Row Level Security: Postgres policies (`auth.uid() = user_id`) that scope every query made with a user JWT. |
| **Defence in depth** | Authorization enforced twice: in Next.js route handlers *and* again by RLS in Postgres (see ADR-0002). |
| **Red / green** | Test states: red = failing test that reproduces the gap; green = implementation that passes it. |
| **Spec / ticket** | Skill-produced planning artifacts: `spec.md` and numbered issue files under `.scratch/<feature>/`. |
