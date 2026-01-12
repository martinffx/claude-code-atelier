---
name: methodology
description: Spec-Driven Development methodology and standards patterns. Use when explaining SDD principles, TDD workflows, architecture patterns, or migrating from SDD v1 to v2.
user-invocable: false
---

# Spec-Driven Development Methodology

Core principles and patterns for Spec-Driven Development.

## Core Principles

### Layered Architecture

Bottom-up dependency flow:

```
Router → Service → Repository → Entity → Database
```

- **Entity**: Domain models, data transformations, validation
- **Repository**: Data access layer, database operations
- **Service**: Business logic, orchestration
- **Router**: HTTP/API layer, request/response handling

### Stub-Driven TDD Workflow

```
Stub → Test → Implement → Refactor
```

1. **Stub**: Create interface/skeleton with mock implementations
2. **Test**: Write tests at layer boundaries
3. **Implement**: Fill in real implementation
4. **Refactor**: Clean up while tests pass

### Layer Boundary Testing

Test at component boundaries, not every method:

- **Router**: HTTP request → mock Service → HTTP response
- **Service**: Entity in → mock Repository → Entity out
- **Repository**: Entity → real database → Entity

### Domain-Driven Design

Entities manage all data transformations:

- `fromRequest()` - Parse/validate HTTP request
- `toRecord()` - Convert to database format
- `toResponse()` - Format for HTTP response
- `validate()` - Business rule validation

## Template Adaptation Strategy

When applying standards to a new project:

1. **Exact Match**: Template exists for detected stack → Use directly
2. **Similar Match**: Template for same language, different framework → Adapt framework parts
3. **Language Match**: Template for language only → Use language patterns, add stack-specific sections
4. **No Match**: Generic standards based on language-agnostic principles

## Language-Agnostic Patterns

These patterns apply regardless of technology stack:

- Dependency injection at boundaries
- Error handling consistency
- Separation of concerns
- Single responsibility principle
- Test isolation

## Standards Documentation

### coding.md

Contains:
- TDD implementation patterns
- Language-specific coding conventions
- Stub-driven workflow examples
- Testing strategies
- Code organization patterns

### architecture.md

Contains:
- Layered architecture patterns
- Dependency injection examples
- Domain-driven design patterns
- Error handling strategies
- Integration patterns

## SDD Version 2

### Key Changes from v1

| Aspect | v1 | v2 |
|--------|----|----|
| Specs | Separate spec.md + design.md | Unified spec.md |
| Tasks | tasks.md file | Beads CLI |
| Metadata | JSON files (requirements, context, plan) | None (streamlined) |

### Migration from v1

Detection indicators:
- `design.md` files
- `tasks.md` files
- `requirements.json`, `context.json`, `plan.json` files

Migration steps:
1. Merge spec.md + design.md → unified spec.md
2. Import tasks from tasks.md → Beads
3. Delete old format files
4. Update .gitignore for Beads

### Beads Integration

Beads provides dependency-aware task tracking:

```bash
bd create "Task description" -p 1 -l feature-name
bd dep add <child-id> <parent-id> --type blocks
bd ready --label feature-name
```

.gitignore entries:
```
# Beads cache
.beads/beads.db
.beads/beads.db-*
.beads/bd.sock

# Keep .beads/beads.jsonl (issue data)
```

## Technology Stack Detection

Detect stack by looking for these files:

| Stack | Detection Files |
|-------|----------------|
| Rust | `Cargo.toml` |
| Node.js/TypeScript | `package.json` |
| Python | `pyproject.toml`, `requirements.txt`, `setup.py` |
| Go | `go.mod` |
| Java | `pom.xml`, `build.gradle` |
| C# | `.csproj`, `packages.config` |
| Ruby | `Gemfile` |
| PHP | `composer.json` |

Analyze:
- Primary programming language
- Web framework
- Database technology
- Testing framework
- Build system

## Implementation Order

Tasks should be ordered by technical dependency (bottom-up):

```
1. Entity (data models, validation)
2. Repository (database layer)
3. Service (business logic)
4. Router (API endpoints)
5. Integration tests
```

This ensures each layer can be tested against its dependencies.
