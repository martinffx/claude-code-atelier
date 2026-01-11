---
name: atelier-spec
description: Feature specification lifecycle management. Use for creating new feature specs (greenfield or brownfield detection), implementing tasks with layer boundary testing, tracking progress via Beads, or syncing specs from existing code.
user-invocable: true
---

# Spec-Driven Development

Manage feature specifications through their complete lifecycle.

## Workflows

| Workflow | When to Use |
|----------|-------------|
| [create](workflows/create.md) | New feature spec (auto-detects greenfield/brownfield) |
| [work](workflows/work.md) | Implement next ready task with stub-test-fix pattern |
| [status](workflows/status.md) | Track feature progress via Beads |
| [sync](workflows/sync.md) | Update spec from code (retroactive documentation) |

## Routing

Analyze the request and load the appropriate workflow:
- Creating a new feature or spec → Read `workflows/create.md`
- Implementing, coding, or working on tasks → Read `workflows/work.md`
- Checking progress, status, or metrics → Read `workflows/status.md`
- Syncing spec from existing code → Read `workflows/sync.md`

## Quick Reference

### Prerequisites
- Beads CLI: `npm install -g @beads/bd && bd init`
- Project structure: `docs/product/`, `docs/spec/`, `docs/standards/`

### Dependency Order
Entity → Repository → Service → Router (bottom-up implementation)

### Layer Boundary Testing
- Router: HTTP request → mock Service → HTTP response
- Service: Entity in → mock Repository → Entity out
- Repository: Entity → real database → Entity
