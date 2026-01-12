# Code

Code quality workflows for reviewing changes and creating commits.

## Commands

| Command | Description |
|---------|-------------|
| /code:review | Review code changes as senior engineer |
| /code:commit | Create well-crafted conventional commits |

## Usage

```bash
/code:review            # Review code changes as senior engineer
/code:commit [message]  # Create well-crafted conventional commit
```

## Quick Reference

### Conventional Commit Types

| Type | Use |
|------|-----|
| feat | New feature |
| fix | Bug fix |
| refactor | Code restructuring |
| test | Test changes |
| docs | Documentation |
| chore | Build/config |

### Review Severity Levels

- **Critical**: Security, data loss, breaking changes
- **Important**: Bugs, performance, patterns
- **Minor**: Naming, style, edge cases

## Shared Agents

For enhanced workflows, install the **spec** plugin which provides shared agents:

| Agent | Purpose |
|-------|---------|
| atelier-architect | Technical design decisions |
| atelier-oracle | Requirements and strategic thinking |
| atelier-clerk | Fast utility tasks |

## Installation

```bash
/plugin marketplace add martinffx/claude-code-atelier
/plugin install code@atelier
/plugin install spec@atelier  # Recommended: provides shared agents
```

## License

MIT
