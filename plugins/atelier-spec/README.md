# Spec

Spec-Driven Development combining [OpenSpec](https://openspec.dev/) living specifications, [AgentOS](https://buildermethods.com/agent-os/workflow) orchestrated delegation, and [Beads](https://github.com/steveyegge/beads) dependency-aware task tracking.

## Three Innovations, One Workflow

| Innovation | From | Contribution |
|------------|------|--------------|
| **Living Specs** | [OpenSpec](https://openspec.dev/) | Specs persist as docs, delta-based changes for brownfield |
| **Orchestrated Delegation** | [AgentOS](https://buildermethods.com/agent-os/workflow) | Subagents (architect, oracle, clerk) with controlled context |
| **Dependency Tracking** | [Beads](https://github.com/steveyegge/beads) | `bd ready` surfaces unblocked tasks, git-backed persistence |

## Workflow

### Greenfield: /spec:create

```
┌─────────────────────────────────────────────────────────────────────┐
│ /spec:create <feature>                                              │
│   ├── @clerk    → Check structure, detect existing code             │
│   ├── @oracle   → Gather requirements (user story, acceptance)      │
│   ├── @architect → Technical design (only needed layers)            │
│   ├── @clerk    → Write docs/spec/<feature>/spec.md                 │
│   └── beads     → Create epic + dependency-ordered tasks            │
└─────────────────────────────────────────────────────────────────────┘
```

### Brownfield: /spec:propose → /spec:work → /spec:complete

```
┌─────────────────────────────────────────────────────────────────────┐
│ /spec:propose <feature> <change>                                    │
│   ├── @clerk    → Load current spec                                 │
│   ├── @oracle   → Lightweight change interview                      │
│   ├── @architect → Generate ADDED/MODIFIED/REMOVED delta            │
│   └── beads     → Create change epic + tasks                        │
├─────────────────────────────────────────────────────────────────────┤
│ /spec:work [feature]                                                │
│   ├── beads     → `bd ready` finds next unblocked task              │
│   ├── [impl]    → Stub → Test → Implement at layer boundary         │
│   └── beads     → `bd close` marks complete                         │
├─────────────────────────────────────────────────────────────────────┤
│ /spec:complete <feature> <change>                                   │
│   ├── @clerk    → Load delta.md                                     │
│   ├── @architect → Merge delta into spec.md                         │
│   └── beads     → Close epic                                        │
└─────────────────────────────────────────────────────────────────────┘
```

## Agents

Specialized subagents invoked via `@agent-name` during command execution.

| Agent | Model | Responsibilities |
|-------|-------|------------------|
| **architect** | opus | Technical design, data modeling, API contracts, Beads task breakdown |
| **oracle** | opus | Requirements interviews, strategic analysis, progress recommendations |
| **clerk** | haiku | Fast context retrieval, file scaffolding, template application |

## Architecture Patterns

### Layered Dependencies (Bottom-Up)

```
Entity → Repository → Service → Router
```

Each layer depends only on layers below. Beads enforces this via `bd dep add`.

### Stub-Driven TDD

```
Stub → Test → Implement → Refactor
```

Test at layer boundaries, not every method.

### Contextual Layer Detection

Only create tasks for layers actually needed. A simple service change doesn't require entity/repository tasks.

## Commands

| Command | Description |
|---------|-------------|
| /spec:create | Create new feature specification (auto-init if needed) |
| /spec:propose | Propose changes to existing feature |
| /spec:sync | Update spec from code changes |
| /spec:work | Implement next ready task |
| /spec:complete | Complete changes and merge delta |
| /spec:status | Track feature progress via Beads |

## Skills

| Skill | Description |
|-------|-------------|
| project-structure | Project structure patterns, initialization guidance (auto-invoked) |
| methodology | SDD principles, TDD workflows, architecture patterns (auto-invoked) |

## Usage

```bash
# Greenfield: Create feature (auto-initializes project on first run)
/spec:create <feature>

# Brownfield: Propose changes to existing feature
/spec:propose <feature> <change>

# Implement tasks (bd ready surfaces next unblocked task)
/spec:work [feature]

# Complete changes and merge delta into spec
/spec:complete <feature> <change>

# Track progress and identify blockers
/spec:status

# Update spec from code changes
/spec:sync <feature>
```

## Prerequisites

- Beads CLI for task tracking: `npm install -g @beads/bd && bd init`
- Project structure: `docs/product/`, `docs/spec/`, `docs/standards/`

## Installation

```bash
/plugin marketplace add martinffx/claude-code-atelier
/plugin install spec@atelier
```

## License

MIT
