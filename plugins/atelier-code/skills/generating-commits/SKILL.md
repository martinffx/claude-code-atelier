---
name: generating-commits
description: Writing clear, conventional commit messages. Use when creating git commits, staging changes, or helping with version control messages. Applies conventional commits format with scope and imperative mood.
user-invocable: false
---

# Commit Message Conventions

## Format

```
type(scope): brief description

[optional body]

[optional footer]
```

## Types

| Type | When to use |
|------|-------------|
| `feat` | New feature, component, endpoint, or capability |
| `fix` | Bug fix, error correction |
| `refactor` | Code restructuring without behavior change |
| `test` | Adding or updating tests |
| `docs` | Documentation changes |
| `chore` | Build, config, dependencies, tooling |
| `style` | Formatting, whitespace (no logic change) |
| `perf` | Performance improvement |

## Scope

Scope identifies the area of change:
- Component/module name: `feat(auth): Add OAuth flow`
- Feature area: `fix(payments): Handle refund edge case`
- File/layer: `refactor(api): Extract validation middleware`

Omit scope for broad changes: `chore: Update dependencies`

## Subject Line Rules

1. **50 characters max** - hard limit for readability
2. **Imperative mood** - "Add" not "Added" or "Adds"
3. **No period** at the end
4. **Lowercase** after type/scope prefix
5. **What**, not how - describe the change, not implementation

## Examples

```
feat(users): Add email verification flow
fix: Resolve race condition in cache invalidation
refactor(api): Extract request validation to middleware
test(auth): Cover token refresh edge cases
docs: Update API authentication guide
chore: Bump TypeScript to 5.4
```

## Multi-change Commits

When a commit touches multiple areas:
- Lead with the **primary change type**
- Mention secondary changes in body if significant

```
feat(orders): Add bulk order creation endpoint

Also updates order validation schema and adds
integration tests for the new endpoint.
```

## Breaking Changes

Use `!` after type/scope and explain in footer:

```
feat(api)!: Change authentication to JWT

BREAKING CHANGE: Bearer token format changed.
Clients must update to new token structure.
```

## Atomic Commits

Each commit should be:
- **Self-contained** - builds and tests pass
- **Single purpose** - one logical change
- **Reviewable** - understandable in isolation

Split large changes into multiple commits when possible.

## Before Committing

1. Review staged changes: `git diff --cached`
2. Verify nothing unintended is included
3. Run tests if touching logic
4. Check commit message follows format
