# Spec

Spec-Driven Development combining [OpenSpec](https://openspec.dev/) living specifications, [AgentOS](https://buildermethods.com/agent-os/workflow) orchestrated delegation, and [Beads](https://github.com/steveyegge/beads) dependency-aware task tracking.

## Three Innovations, One Workflow

| Innovation | From | Contribution |
|------------|------|--------------|
| **Living Specs** | [OpenSpec](https://openspec.dev/) | Specs persist as docs, delta-based changes for brownfield |
| **Orchestrated Delegation** | [AgentOS](https://buildermethods.com/agent-os/workflow) | Subagents (architect, oracle, clerk) with controlled context |
| **Dependency Tracking** | [Beads](https://github.com/steveyegge/beads) | `bd ready` surfaces unblocked tasks, git-backed persistence |

## Workflow

### Greenfield

```
/spec:init → /spec:create → /spec:design → /spec:plan → /spec:work → /spec:complete
```

**Flow:**
1. `/spec:init` - Initialize project structure
2. `/spec:create <feature>` - Gather requirements (user story, acceptance criteria)
3. `/spec:design <feature>` - Generate technical design (architecture, domain model, APIs)
4. `/spec:plan <feature>` - Create implementation plan + Beads epic with tasks
5. `/spec:work [feature]` - Implement next ready task (Stub → Test → Fix)
6. `/spec:complete <feature> initial` - Mark feature complete

### Brownfield

```
/spec:propose → /spec:design → /spec:plan → /spec:work → /spec:complete
```

**Flow:**
1. `/spec:propose <feature> <change>` - Propose changes (motivation, affected components)
2. `/spec:design <feature> <change>` - Generate technical design for changes
3. `/spec:plan <feature> <change>` - Create implementation plan + Beads epic
4. `/spec:work [feature]` - Implement next ready task
5. `/spec:complete <feature> <change>` - Merge delta into main spec

## Agents

Specialized subagents invoked via `@agent-name` during command execution.

| Agent | Model | Responsibilities |
|-------|-------|------------------|
| **architect** | opus | Technical design, data modeling, API contracts, Beads task breakdown |
| **oracle** | opus | Requirements interviews, strategic analysis, progress recommendations |
| **clerk** | haiku | Fast context retrieval, file scaffolding, template application |

## Skills vs Agents

Two complementary systems work together:

| Concept | Invocation | Purpose | Examples |
|---------|------------|---------|----------|
| **Agents** | Explicit via `@agent-name` | Execute tasks during commands | @clerk, @oracle, @architect |
| **Skills** | Auto-invoked by context | Provide domain knowledge | product, architect, testing |

**Agents** are personas that perform actions during command execution (e.g., @oracle conducts interviews, @architect designs systems).

**Skills** are contextual knowledge auto-loaded when relevant (e.g., architect skill provides DDD patterns when designing, testing skill provides TDD guidance when writing tests).

Skills flow through the specification process:

```
Product → Architect → Testing
  │           │           │
  ▼           ▼           ▼
Scope &    Data Model   Test
Stories    & APIs       Strategy
```

## Architecture Patterns

### Functional Core / Effectful Edge

```
Bounded Context
┌────────────────────────────────────────────────────────────────┐
│   Effectful Edge (IO)              Functional Core (Pure)      │
│   ┌──────────────────┐             ┌──────────────────┐        │
│   │ Router           │────────────▶│ Service          │        │
│   │ Repository       │◀────────────│ Entity/Aggregate │        │
│   │ Consumer/Producer│◀── Events ──│                  │        │
│   └──────────────────┘             └──────────────────┘        │
└────────────────────────────────────────────────────────────────┘
```

**Key Principle:** Business logic lives in the functional core (Service + Entity). IO operations live in the effectful edge. Core defines interfaces; edge implements them (dependency inversion).

### DDD Patterns

- **Bounded Context** - Module boundary containing all layers for a domain
- **Aggregates** - Entity clusters with a root that enforces invariants
- **Value Objects** - Immutable objects defined by attributes, not identity
- **Domain Events** - Cross context boundaries via Producer/Consumer

### Stub-Driven TDD

```
Stub → Test → Implement → Refactor
```

Test at layer boundaries: Core (unit tests with stubs) vs Edge (integration tests).

## Commands

| Command | Description |
|---------|-------------|
| /spec:init | Initialize repository for Spec-Driven Development |
| /spec:create | Gather requirements for new feature |
| /spec:design | Generate technical design |
| /spec:plan | Create implementation plan + Beads epic |
| /spec:propose | Propose changes to existing feature |
| /spec:work | Implement next ready task |
| /spec:complete | Merge changes into main spec |
| /spec:status | Track progress via Beads |
| /spec:sync | Update spec from code changes |

## Skills

| Skill | Description |
|-------|-------------|
| project-structure | Directory layout implementing 3-layer context model |
| methodology | AgentOS context layers, orchestrated delegation, living specs |
| product | Requirements discovery, scope definition, user story extraction |
| architect | DDD and hexagonal architecture with functional core pattern |
| testing | Stub-Driven TDD and layer boundary testing strategy |

## Usage

```bash
# Initialize project for SDD (one-time setup)
/spec:init

# Greenfield: New feature workflow
/spec:create <feature>           # Gather requirements
/spec:design <feature>            # Generate technical design
/spec:plan <feature>              # Create implementation plan
/spec:work [feature]              # Implement tasks
/spec:complete <feature> initial  # Mark complete

# Brownfield: Change existing feature workflow
/spec:propose <feature> <change>    # Propose changes
/spec:design <feature> <change>     # Design changes
/spec:plan <feature> <change>       # Plan implementation
/spec:work [feature]                # Implement tasks
/spec:complete <feature> <change>   # Merge delta

# Track progress and identify blockers
/spec:status [feature]

# Update spec from code changes
/spec:sync <feature>
```

## Prerequisites

- Initialize project structure: Run `/spec:init` (sets up `docs/product/`, `docs/spec/`, `docs/standards/`)
- Beads CLI for task tracking (optional): `npm install -g @beads/bd`

## Installation

```bash
/plugin marketplace add martinffx/claude-code-atelier
/plugin install spec@atelier
```

## License

MIT
