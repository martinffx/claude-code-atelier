# Spec

Feature specification workflows for Spec-Driven Development. Includes 6 commands and 2 skills for comprehensive SDD support.

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
# Create feature (auto-initializes project on first run)
/spec:create <feature>

# Propose changes to existing feature
/spec:propose <feature> <change>

# Implement tasks
/spec:work <feature>

# Complete changes
/spec:complete <feature> <change>

# Track progress
/spec:status

# Update spec from code
/spec:sync <feature>
```

## Agents

Specialized agents for SDD workflows. Claude delegates to these automatically based on task context.

| Agent | Model | Purpose |
|-------|-------|---------|
| atelier-architect | opus | Technical design, data modeling, API design, task breakdown |
| atelier-oracle | opus | Requirements gathering, strategic thinking, progress tracking |
| atelier-clerk | haiku | Fast utility tasks, file operations, context retrieval |

View available agents with `/agents`.

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
