# Code

Code quality workflows for reviewing changes, creating commits, and validating builds.

## Skills

| Skill | Type | Description |
|-------|------|-------------|
| atelier-review | User-invocable | Review code changes as senior engineer |
| atelier-commit | User-invocable | Create well-crafted conventional commits |
| reviewing-code | Reference | Code review patterns (auto-loaded) |
| generating-commits | Reference | Commit message conventions (auto-loaded) |

## Usage

```bash
/atelier-review            # Review code changes as senior engineer
/atelier-commit [message]  # Create well-crafted conventional commit
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

## Installation

```bash
/plugin marketplace add martinffx/claude-code-atelier
/plugin install code@atelier
```

## License

MIT
