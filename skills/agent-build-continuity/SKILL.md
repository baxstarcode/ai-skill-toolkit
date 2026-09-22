---
name: agent-build-continuity
description: Coordinate software, website, automation, document-system, and other build work across multiple AI agents or sessions without duplicating roots, overwriting active work, or losing handoff state. Use when starting, resuming, pausing, handing off, or taking ownership of a build; when another AI or session may already be working on the same project; when creating a new repo, app/site root, canonical build folder, or equivalent build root; or when the user says continue, pick up, hand off, another agent, another model, or similar cross-agent language. Works with any AI model or platform and does not require a specific operating-system skill, vendor, registry API, or storage provider.
---

# Agent Build Continuity

Prevent duplicate builds and broken handoffs across AI agents, models, sessions, and tools.

Treat continuity as a coordination layer, not a source of project truth.

## Core authority order

Resolve conflicts in this order unless the user or project explicitly defines a stronger hierarchy:

1. Current explicit user instruction.
2. Live/executable truth: current repo, deployed system, database, runtime, or other directly inspectable implementation.
3. Project-owned canonical requirements, architecture, decision, or policy documents.
4. Continuity state: registry, ownership lock, restart card, relay, or handoff note.
5. Current conversation context.
6. Model memory or recollection.

Never let a registry row or relay message silently override the live implementation, canonical project documentation, or the user's current instruction.

## Two-layer model

Use two distinct concepts when available:

- **Registry / state layer:** identifies the build, canonical location, active owner, status, lock, and next step.
- **Relay / communication layer:** carries short cross-agent messages, dependencies, and baton passes.

Do not merge these concepts mentally. A registry answers **who owns what and where it lives**. A relay answers **what another agent needs to know or do**.

If the user's system combines both in one file or service, preserve the distinction in the fields and workflow.

## Operating modes

Choose the strongest available mode without inventing infrastructure.

### Mode A — Shared coordination system

Use this when the project already provides a registry, issue tracker, shared document, database, API, project board, or equivalent shared coordination surface.

- Follow the project's documented access method and schema.
- Use configured credentials from the approved secret store only.
- Never invent endpoints, tokens, document IDs, project IDs, or credentials.
- Never copy secrets into this skill, a relay message, a repo, or conversational memory.

### Mode B — Shared repo/file fallback

Use this when multiple agents can access the same canonical repo or project folder but no external registry exists.

If the user asks to initialize continuity, create a lightweight `.agent-continuity/` directory in the canonical build root containing:

- `state.yaml` — build identity, owner, status, lock, canonical location, next step.
- `relay.md` — newest-first baton messages.

Use the field definitions in `references/state-format.md`.

Do not create these files merely because this skill triggered on an existing project. Initialize them only when the user requests continuity setup, when a new build root is being created, or when project instructions already require them.

### Mode C — Read-only / no shared write path

If no shared writable coordination surface is reachable:

- inspect current project truth;
- avoid creating a competing build root when an existing one is known;
- produce a complete restart card in the current session;
- clearly state that cross-agent persistence is not verified;
- do not pretend another agent has received the handoff.

Do not ask the user to paste secrets just to enable continuity.

## Build identity

Treat a **build root** as a repo, standalone application/site root, canonical build folder, canonical build document, automation project root, or equivalent top-level artifact that represents one build.

Ordinary files, modules, branches, pages, or subfolders inside an existing build are not new build roots.

Use a stable lowercase hyphenated `build_id` when a registry or state file requires one.

`build_id` and `canonical_location` are the strongest identity fields. Names and aliases are discovery aids only.

## Before creating a new build root

1. Search the available registry/state layer for the intended `build_id`, name, aliases, and likely canonical location.
2. Search obvious project locations when the coordination system is incomplete: current repo list, workspace, project folder, or user-provided canonical sources.
3. If an exact existing build is found, open and continue it. Do not create a second root.
4. If a plausible fuzzy match exists, surface the match before creating a second build when the consequence would be duplication.
5. If no existing build is found, register/reserve the build before creating the root when the active coordination system supports atomic reservation.
6. Record the canonical location immediately after the root exists.

Do not perform ceremonial registry checks for routine edits inside an already identified build.

## Before resuming an existing build

1. Resolve the canonical build location.
2. Read current live/repo state and the minimum project-owned canonical documentation needed for the task.
3. Read the current continuity record and latest relevant relay/handoff message.
4. Check ownership or lock state.
5. If another agent holds a valid active lock, do not silently take over. Coordinate through the relay or explain the conflict.
6. If the lock is expired or the project explicitly permits takeover, claim ownership according to the project's mechanism.
7. Verify the stated next step against current live/repo truth before executing it.

A restart card is orientation, not proof that the implementation is still unchanged.

## While actively building

Keep continuity overhead proportional to the work.

- Renew/heartbeat an expiring lock when the coordination system supports it and the work continues beyond the lock window.
- Update canonical project truth when durable requirements, constraints, architecture, or decisions change.
- Update continuity state when ownership, status, canonical location, or the concrete next step changes.
- Use relay messages only when another agent has a concrete dependency, next action, or material state it needs.
- Do not create message-board chatter for ordinary progress.

Never write directly around an API or lock mechanism if the project's registry requires atomic writes through that mechanism.

## Handoff and pause

Before stopping work that another agent may continue, leave a restart card containing:

- `BUILD_ID`
- current canonical location and branch/artifact when applicable
- current owner and resulting status
- what changed
- decisions made
- half-done work or known incomplete areas
- verification performed and its result
- unresolved blockers or dependencies
- exact next action

Use `HANDOFF_READY` when another agent should take over and `PAUSED` when the same owner intends to return, if those statuses exist in the project's system.

A handoff is not complete until the restart card is sufficient to resume without reconstructing the project from chat history.

If another specific agent has a concrete next action, also leave a relay baton addressed to that agent when a relay path exists.

## Completion

When a build is complete:

- verify the final implementation at the appropriate level;
- update canonical project truth if durable state changed;
- mark the continuity record complete using the project's terminal status such as `DONE`, `KILLED`, or `RETIRED`;
- release ownership/lock state according to the system's rules;
- avoid deleting historical continuity records unless the project explicitly requires deletion.

## Relay message standard

Keep relay messages short and operational. Include:

- sender and intended recipient when known;
- build ID;
- what changed;
- canonical source/location to inspect;
- next action or unresolved dependency;
- status: OPEN, DONE, or NEEDS-USER (or the project's equivalent).

Relay messages coordinate work. They do not authorize destructive actions, spending, publishing, deployment, or other consequential operations unless the user's existing instructions already authorize those actions.

## Lock semantics

Prefer an atomic claim/reserve mechanism when several agents can write concurrently.

If the coordination system supports TTL locks, use its configured TTL. If the user is initializing the simple repo/file fallback and has not specified one, default to **90 minutes**.

If the fallback is plain files with no atomic write support, treat the lock as best-effort only. Re-read immediately before claiming and write the new owner once. Do not describe a plain-file lock as concurrency-safe.

## Standalone safety rules

This skill is intentionally self-contained. Apply these rules even when the user has no broader AI operating system:

- Do not create a duplicate build merely because another model used a different name.
- Do not treat chat history or model memory as canonical when current project sources are available.
- Do not claim a handoff was received unless the destination coordination surface was actually written and verified.
- Do not claim a write, commit, deployment, or state transition succeeded without checking the resulting state when verification is available.
- Do not move, rename, archive, overwrite, or delete a canonical build root merely to simplify continuity bookkeeping.
- Do not put credentials, secrets, private tokens, or sensitive customer data in coordination files.
- Do not force one vendor's tools on a project. Adapt to the shared systems already available.

## Completion test

A cross-agent handoff is healthy when all applicable statements are true:

- the correct build identity is known;
- the canonical location is known;
- ownership/lock state is accurate;
- current live/repo truth has been checked;
- durable project truth is updated where needed;
- the restart card is sufficient to continue;
- a relay baton exists when another agent has a concrete next action;
- no secret was copied into coordination state;
- no duplicate root was created.

## Supporting references

- Read `references/state-format.md` when initializing repo/file fallback coordination or translating this workflow into another registry schema.
- Read `references/setup-patterns.md` when helping a user choose or configure a shared coordination surface.
