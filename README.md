# Claude Code Atelier

![Atelier - A collaborative workshop for software development](atelier.jpg)

> An atelier is the private workshop or studio where a principal master and a number of assistants, students, and apprentices can work together producing fine art or visual art released under the master's name or supervision. 
> [Wikipedia](https://en.wikipedia.org/wiki/Atelier)

A software development atelier for Claude Code - spec-driven development, code quality, deep thinking, and TypeScript patterns.

## Plugins

### [spec](plugins/atelier-spec/README.md) - Spec-Driven Development

- **Lightweight documentation over heavyweight planning** - Replace PRDs with minimal business context + detailed implementation specs
- **Dependency-driven over sprint-driven** - Order tasks by technical requirements (Entity → Repository → Service → Router)
- **AI-assisted implementation** - Structured specs enable AI agents to make informed technical decisions without constant prompting
- **Prevention over debugging** - Spot issues in design, not production
- **Enforced patterns** - Layered architecture with domain-driven design and layer boundary testing

**What you get:** 9 commands + 5 skills + 3 specialized agents (architect, oracle, clerk)

```bash
# Greenfield workflow
/spec:create <feature>   # Gather requirements
/spec:design <feature>   # Generate technical design
/spec:plan <feature>     # Create implementation plan with Beads epic
/spec:work [feature]     # AI-driven implementation with stub→test→fix workflow

# Brownfield workflow
/spec:propose <feature> <change>  # Propose changes to existing feature
/spec:design <feature> <change>   # Design changes
/spec:plan <feature> <change>     # Plan implementation
/spec:work [feature]              # Implement changes

# Progress tracking
/spec:status [feature]   # Track progress via Beads
```

[→ Full spec plugin documentation](plugins/atelier-spec/README.md)

---

### [code](plugins/atelier-code/README.md) - Code Quality

**Code reviews and conventional commits** that follow your project standards.

**What it does:**
- Reviews code changes against architectural patterns and standards
- Identifies security vulnerabilities, performance issues, maintainability concerns
- Generates conventional commits with proper scope and detailed context
- Enforces consistency across your codebase

```bash
/code:review             # Get structured, relevant perspective on changes
/code:commit [message]   # Create well-crafted conventional commit
```

[→ Full code plugin documentation](plugins/atelier-code/README.md)

---

### [oracle](plugins/atelier-oracle/README.md) - Deep Thinking

**Spicier, structured thinking and reasoning** for complex problems that need deeper analysis.

**When to use:**
- Complex bugs that require investigation across multiple layers
- Architecture decisions with multiple trade-offs
- Performance bottlenecks that need systematic profiling
- Problems where the root cause isn't immediately obvious

```bash
/oracle:debug <error>    # Systematic debugging with bisect methodology
```

Plus 2 auto-invoked skills: `atelier-challenge` (critical thinking), `atelier-thinkdeep` (extended reasoning)

[→ Full oracle plugin documentation](plugins/atelier-oracle/README.md)

---

### [typescript](plugins/atelier-typescript/README.md) - TypeScript Patterns

**Production-ready patterns** for the TypeScript ecosystem - **automatically loaded** when relevant.

**Coverage:**
- **REST API design** - Resource naming, HTTP methods, error responses, pagination, versioning
- **DynamoDB Toolbox v2** - Single-table design, entity definitions, GSI patterns, queries
- **Drizzle ORM** - Type-safe SQL for PostgreSQL/MySQL/SQLite/Cloudflare D1/Durable Objects
- **Fastify + TypeBox** - Route handlers, validation, type-safe APIs
- **Build Tools** - Bun, tsgo, Vitest, Biome, Turborepo configurations

No commands needed - patterns are auto-invoked when working with these technologies.

[→ Full typescript plugin documentation](plugins/atelier-typescript/README.md)

---

### [python](plugins/atelier-python/README.md) - Python Patterns

**Modern Python ecosystem patterns** - architecture, monorepos, tooling, APIs, databases, and testing - **automatically loaded** when relevant.

**Coverage:**
- **Architecture** - Functional core/imperative shell, DDD patterns, layered architecture
- **Monorepo** - uv workspaces, mise task orchestration, apps/packages pattern
- **FastAPI** - REST APIs, Pydantic validation, dependency injection, OpenAPI
- **Testing** - Stub-Driven TDD, layer boundary testing, pytest patterns
- **SQLAlchemy** - ORM patterns, queries, async, upserts
- **Temporal** - Workflow orchestration, activities, error handling
- **Modern Python** - Type hints, generics, async/await, pattern matching
- **Build Tools** - uv, mise, ruff, basedpyright, pytest configurations

No commands needed - patterns are auto-invoked when working with these technologies.

[→ Full python plugin documentation](plugins/atelier-python/README.md)

## Installation

```bash
# Add the marketplace
/plugin marketplace add martinffx/claude-code-atelier

# Install plugins
/plugin install spec@atelier
/plugin install code@atelier
/plugin install oracle@atelier
/plugin install typescript@atelier
/plugin install python@atelier
```

## Development

For local development, use `--plugin-dir` to load plugins directly:

```bash
# Load entire marketplace
claude --plugin-dir ./claude-code-atelier

# Load individual plugins
claude --plugin-dir ./claude-code-atelier/plugins/atelier-spec
claude --plugin-dir ./claude-code-atelier/plugins/atelier-code
claude --plugin-dir ./claude-code-atelier/plugins/atelier-oracle
claude --plugin-dir ./claude-code-atelier/plugins/atelier-typescript
claude --plugin-dir ./claude-code-atelier/plugins/atelier-python
```

Restart Claude Code after making changes to reload plugins.

## License

MIT Copyright (c) 2026 Martin Richards
