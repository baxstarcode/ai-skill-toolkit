# Setup patterns

Choose the lightest shared system that every participating agent can reliably read and, when needed, write.

## Existing project tracker or database

Best when a team already has a shared system with atomic updates.

Map the conceptual fields from `state-format.md` into the existing system. Do not create a second registry just because this skill uses different names.

## GitHub or another shared code host

Good for software teams when all agents can access the same repository.

Possible implementations:

- one project issue labeled `continuity`;
- a small machine-readable state file plus a relay markdown file;
- a project board/card with owner, status, and next-step fields.

Prefer a method that survives sessions and is visible to humans.

## Shared document, sheet, or database

Good when several models can access the same workspace but do not share a repo.

Use one row/record per build root. Preserve a stable `build_id`. Add atomic locking when the platform supports it.

## API-backed registry

Best when concurrent autonomous agents frequently operate on the same projects.

Useful operations include:

- check
- reserve
- claim
- heartbeat
- handoff
- pause
- release
- finish
- list

Keep authentication outside the skill and outside the registry data itself. Prefer per-agent credentials or another least-privilege mechanism supported by the environment.

## No shared system yet

Do not block useful work solely because the user has not built infrastructure.

For one existing shared repo, the `.agent-continuity/` fallback is sufficient to test the workflow. If the user later experiences real concurrency conflicts, migrate the same fields to an atomic shared service.

Do not build a message broker, dashboard, database, or webhook layer before the workflow demonstrates that simpler shared state is insufficient.
