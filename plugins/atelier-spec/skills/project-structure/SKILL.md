---
name: project-structure
description: Project structure patterns for Spec-Driven Development projects. Use when setting up new projects, organizing documentation, or explaining SDD directory structure.
user-invocable: false
---

# Project Structure Patterns

Standard directory structure for Spec-Driven Development projects.

## Core Structure

```
project/
├── docs/
│   ├── product/          # Product-level documentation
│   ├── spec/             # Feature specifications
│   └── standards/        # Technical standards
└── CLAUDE.md            # Project overview and commands
```

## docs/product/

Product-level documentation:

- **product.md** - Product definition, target users, core features, success metrics
- **roadmap.md** - Next 3 features in priority order, implementation strategy, current status

## docs/spec/

Feature specifications organized by feature name:

```
docs/spec/
└── <feature-name>/
    └── spec.md          # Unified spec (requirements + technical design)
```

## docs/standards/

Technical standards adapted for the project's technology stack:

- **coding.md** - TDD implementation patterns, coding principles
- **architecture.md** - Architecture patterns, design principles

## Technology Stack Detection

When initializing a project, detect the stack by looking for these files:

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
- Web framework (if any)
- Database technology (if detectable)
- Testing framework (if detectable)
- Build system and package manager

## Language-Agnostic Principles

These principles apply regardless of technology stack:

- **Layered architecture**: Router → Service → Repository → Entity → Database
- **Stub-driven TDD**: Stub → Test → Implement → Refactor
- **Domain-driven design**: Entities manage data transformations
- **Dependency injection**: Inject dependencies at boundaries
- **Error handling**: Consistent error patterns

## Multi-Stack Support

For projects with multiple technology stacks:

- Separate standards directories per stack
- Shared `docs/product/` documentation
- Stack-specific implementation notes in specs
- Generic standards apply across all stacks
- Template adaptation applied per stack independently

## Initialization Checklist

When setting up a new project:

1. ✓ Gather product definition and target users
2. ✓ Detect technology stack
3. ✓ Create directory structure
4. ✓ Generate product.md and roadmap.md
5. ✓ Create coding.md and architecture.md standards
6. ✓ Generate CLAUDE.md with project overview
