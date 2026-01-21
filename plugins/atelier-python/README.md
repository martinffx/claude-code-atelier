# atelier-python

Modern Python ecosystem patterns plugin for Claude Code Atelier - architecture, monorepos, tooling, APIs, databases, and testing.

## Skills

### 1. architecture
**Auto-invoked for:** Python application architecture, functional core/imperative shell, DDD, data modeling

**Covers:**
- Functional core / imperative shell pattern
- Domain-Driven Design (entities, value objects, aggregates, repositories)
- dataclasses and Pydantic for domain models
- Layered architecture (Router → Service → Repository → Entity → Database)

**References:**
- `functional-core.md` - Pure functions and effect isolation
- `ddd.md` - Domain-Driven Design patterns in Python
- `data-modeling.md` - dataclasses, Pydantic, attrs patterns

### 2. monorepo
**Auto-invoked for:** Python monorepos, uv workspaces, mise, apps/packages pattern

**Covers:**
- uv workspace configuration and commands
- mise.toml for Python version + task orchestration
- apps/ vs packages/ directory pattern
- Namespace packages (PEP 420)
- Docker builds for workspace packages

**References:**
- `docker.md` - Multi-stage Docker builds for workspaces
- `namespace-packages.md` - PEP 420 namespace patterns

### 3. build-tools
**Auto-invoked for:** Python tooling, uv, mise, ruff, basedpyright, pytest

**Covers:**
- uv package management and commands
- mise.toml configuration and tasks
- ruff linting and formatting
- basedpyright strict type checking
- pyproject.toml configuration

**References:**
- `uv.md` - Advanced uv patterns (scripts, workspaces, pip interface)
- `ruff.md` - Ruff rules and configuration
- `basedpyright.md` - Strict typing configuration

### 4. fastapi
**Auto-invoked for:** FastAPI, REST APIs, Pydantic validation, OpenAPI

**Covers:**
- FastAPI app setup and routers
- Pydantic models for request/response
- Dependency injection patterns
- Error responses (RFC 7807 Problem Details)
- Pagination, filtering, sorting

**References:**
- `dependencies.md` - Dependency injection patterns
- `middleware.md` - Middleware and lifecycle hooks
- `validation.md` - Pydantic validation in FastAPI
- `api-design.md` - REST patterns and OpenAPI

### 5. testing
**Auto-invoked for:** pytest, TDD, testing strategies

**Covers:**
- Stub-Driven TDD workflow (Stub → Test → Implement → Refactor)
- Layer boundary testing patterns
- What to test at each layer (Entity, Service, Repository, Router)
- pytest fixtures and parametrized tests
- Mocking with pytest-mock

**References:**
- `pytest.md` - pytest configuration, fixtures, markers
- `boundaries.md` - Layer boundary testing patterns
- `mocking.md` - Stubbing strategies with pytest-mock

### 6. sqlalchemy
**Auto-invoked for:** SQLAlchemy ORM, database access

**Covers:**
- SQLAlchemy 2.0 declarative models
- Session management and transactions
- Query patterns (select, join, aggregate)
- Upsert patterns (on_conflict_do_update)
- Async SQLAlchemy with asyncio

**References:**
- `models.md` - Declarative model patterns
- `queries.md` - Query building and optimization
- `async.md` - Async SQLAlchemy patterns

### 7. temporal
**Auto-invoked for:** Temporal workflow orchestration

**Covers:**
- Worker setup and configuration
- Workflow definition patterns
- Activity implementation
- Error handling and retries
- Signals and queries

**References:**
- `testing.md` - Temporal testing patterns
- `patterns.md` - Common workflow patterns (saga, fan-out, etc.)

### 8. modern-python
**Auto-invoked for:** Modern Python features, typing, async/await

**Covers:**
- Type hints (PEP 484, 585, 604)
- Generics with TypeVar and ParamSpec
- Protocol for structural typing
- Pattern matching (match/case)
- Async/await patterns

**References:**
- `typing.md` - Type hints and generics deep dive
- `async.md` - Async/await patterns and best practices
- `pattern-matching.md` - Structural pattern matching

## Installation

### From Marketplace

```bash
# Add marketplace
/plugin marketplace add martinffx/claude-code-atelier

# Install plugin
/plugin install python@atelier
```

### Local Development

```bash
# Load plugin
claude --plugin-dir ./claude-code-atelier/plugins/atelier-python
```

## Usage

Skills are automatically loaded when relevant. Ask Claude questions about:

- "How do I structure a Python monorepo with uv?"
- "Show me FastAPI dependency injection patterns"
- "What's the best way to test a service layer?"
- "How do I implement DDD patterns in Python?"
- "Set up strict type checking with basedpyright"
- "Create async SQLAlchemy queries"
- "Build a Temporal workflow with saga pattern"

The relevant skill will be auto-invoked with comprehensive patterns and best practices.

## Philosophy

This plugin follows modern Python development principles:

1. **Functional Core / Imperative Shell** - Separate pure business logic from side effects
2. **Layered Architecture** - Router → Service → Repository → Entity → Database
3. **Domain-Driven Design** - Rich domain models with business logic
4. **Type Safety** - Strict type checking with basedpyright
5. **Test at Boundaries** - Test component interfaces, not internals
6. **Modern Tooling** - uv, mise, ruff, basedpyright for fast development

## Related Plugins

- **atelier-spec** - Spec-Driven Development workflows
- **atelier-code** - Code quality and review workflows
- **atelier-oracle** - Deep thinking for complex problems
- **atelier-typescript** - TypeScript ecosystem patterns

## License

MIT
