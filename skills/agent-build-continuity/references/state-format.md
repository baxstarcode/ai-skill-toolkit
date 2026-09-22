# Generic continuity state format

Use this as a conceptual schema. Adapt field names to an existing registry instead of creating a competing schema.

## Single-build state

```yaml
version: 1
build_id: example-project
name: Example Project
aliases:
  - old-project-name
canonical_location: github.com/org/repo
active_owner: agent-name-or-session
status: ACTIVE
lock_until: 2026-09-22T17:30:00-05:00
last_touched: 2026-09-22T16:15:00-05:00
next_step: Run the production smoke test, then update the release notes.
```

Recommended statuses:

- `ACTIVE` — currently being worked.
- `PAUSED` — owner intends to return; restart card should exist.
- `HANDOFF_READY` — available for another agent to claim.
- `DONE` — completed.
- `KILLED` — intentionally abandoned.
- `RETIRED` — superseded by another build.

## Restart card

```markdown
BUILD_ID: example-project
CANONICAL_LOCATION: github.com/org/repo
OWNER: agent-a
STATUS: HANDOFF_READY

WHAT CHANGED
- ...

DECISIONS
- ...

INCOMPLETE / BLOCKED
- ...

VERIFICATION
- Tests: ...
- Live/deploy check: ...

NEXT ACTION
- ...
```

## Relay entry

```markdown
[2026-09-22T16:20:00-05:00] AGENT-A -> AGENT-B
BUILD: example-project
STATUS: OPEN
WHAT CHANGED: ...
SOURCE: ...
NEXT: ...
```

## Plain-file fallback

If `.agent-continuity/state.yaml` and `.agent-continuity/relay.md` are used inside a shared repo:

- keep `state.yaml` to one current record for that build;
- put newest relay entries first;
- never store secrets in either file;
- commit the files if all participating agents use the repo as their shared coordination surface;
- treat ownership locks as best-effort unless commits/merges are protected by an external atomic mechanism.
