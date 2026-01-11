---
name: code
description: Code quality workflows for reviewing changes and creating commits. Use when doing code reviews, checking PR quality, or creating git commits.
user-invocable: true
allowed-tools: Bash(git:*)
---

# Code Quality Workflows

Ensure code quality through systematic review and conventional commits.

## Workflows

| Workflow | When to Use |
|----------|-------------|
| [review](workflows/review.md) | Review code changes as a senior engineer |
| [commit](workflows/commit.md) | Create well-crafted conventional commits |

## Routing

Analyze the request and load the appropriate workflow:
- Reviewing code, checking quality, or auditing changes → Read `workflows/review.md`
- Creating commits, staging changes, or committing → Read `workflows/commit.md`

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
- **🔴 Critical**: Security, data loss, breaking changes
- **🟡 Important**: Bugs, performance, patterns
- **🟢 Minor**: Naming, style, edge cases
- **⚪ Skip**: Linter catches, preferences
