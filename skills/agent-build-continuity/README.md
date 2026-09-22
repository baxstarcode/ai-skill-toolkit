# Agent Build Continuity

A model-agnostic coordination skill for people using multiple AI agents or sessions on the same build.

## Download

Use the packaged version in [`../../downloads/agent-build-continuity/skill.zip`](../../downloads/agent-build-continuity/skill.zip).

## What it prevents

- duplicate repositories or build roots
- 2 agents unknowingly modifying the same work
- stale handoffs overriding current repo truth
- losing the exact next step when switching models/sessions
- pretending another agent received a handoff when no shared write actually occurred

## Core files

- [`SKILL.md`](SKILL.md) — main operating workflow
- [`references/setup-patterns.md`](references/setup-patterns.md) — options for shared coordination surfaces
- [`references/state-format.md`](references/state-format.md) — generic registry/restart-card schema

The skill is intentionally standalone and does not assume a separate operating-system skill or a specific vendor stack.
