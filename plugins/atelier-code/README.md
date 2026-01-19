# Code

**Get a senior engineer's perspective on every commit.**

Stop shipping slop to colleagues. Catch vulnerabilities, data exposure risks, and breaking changes before they reach production.

## Why This Matters

Code reviews are where quality happens. But most teams lack the bandwidth for thorough reviews, and solo developers miss the feedback loop entirely. This plugin fills that gap with:

- **Architectural awareness** - Reviews against layered architecture patterns (Router, Service, Repository, Entity)
- **Security-first thinking** - Catches vulnerabilities, data exposure risks, and breaking changes
- **Standards enforcement** - Validates against your project's coding and architecture standards
- **Actionable feedback** - Specific line references with concrete improvement suggestions

## Commands

| Command | Description |
|---------|-------------|
| /code:review | Review code changes as senior engineer |
| /code:commit | Create well-crafted conventional commits |

## Workflow Examples

### Code Review

```bash
# Make your changes, then request a review
/code:review
```

The review examines all staged and unstaged changes, producing a structured report:

```
Summary: Adding user authentication endpoints

Issues by Severity:

  CRITICAL:
  - src/auth/handler.ts:45 - Password stored in plain text
    Fix: Use bcrypt.hash() before storing

  IMPORTANT:
  - src/auth/service.ts:23 - Missing input validation
    Fix: Add zod schema validation for email/password

  MINOR:
  - src/auth/types.ts:12 - Consider renaming 'data' to 'credentials'

Positive Aspects:
- Clean separation between handler and service layers
- Good use of dependency injection

Overall Assessment: REQUEST CHANGES
```

### Smart Commits

```bash
# Auto-generate message from changes
/code:commit

# Or provide your own message
/code:commit "add user authentication"
```

The commit command analyzes your changes and generates a conventional commit message:

```
Suggested: feat(auth): add user authentication endpoints

Committed! Ready for git push
```

## The Senior Engineer Perspective

Reviews focus on what matters most:

**Critical Issues** - Security vulnerabilities, data loss risks, breaking changes. These block merging.

**Important Issues** - Bugs, performance problems, pattern violations. These need attention.

**Minor Issues** - Style, naming, edge cases. Suggestions for improvement.

**Architecture Compliance** - Every review validates against the layered architecture pattern:

```
Router -> Service -> Repository -> Entity -> Database
```

If your project has standards files (`docs/standards/coding.md`, `docs/standards/architecture.md`), reviews validate against them automatically.

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
