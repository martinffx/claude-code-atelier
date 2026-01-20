# Claude Code Atelier

![Atelier - A collaborative workshop for software development](atelier.jpg)

A software development atelier for Claude Code - spec-driven development, code quality, deep thinking, and TypeScript patterns.

## Plugins

### [spec](plugins/atelier-spec/README.md) - Spec-Driven Development

- **Lightweight documentation over heavyweight planning** - Replace PRDs with minimal business context + detailed implementation specs
- **Dependency-driven over sprint-driven** - Order tasks by technical requirements (Entity → Repository → Service → Router)
- **AI-assisted implementation** - Structured specs enable AI agents to make informed technical decisions without constant prompting
- **Prevention over debugging** - Spot issues in design, not production
- **Enforced patterns** - Layered architecture with domain-driven design and layer boundary testing

**What you get:** 6 commands + 2 auto-invoked skills + 3 specialized agents (architect, oracle, clerk)

```bash
/spec:create <feature>   # Greenfield: Create new feature spec with Beads epic
/spec:propose <change>   # Brownfield: Propose changes to existing features
/spec:work <feature>     # AI-driven implementation with stub→test→fix workflow
/spec:status             # Track progress across all features
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

## Installation

```bash
# Add the marketplace
/plugin marketplace add martinffx/claude-code-atelier

# Install plugins
/plugin install spec@atelier
/plugin install code@atelier
/plugin install oracle@atelier
/plugin install typescript@atelier
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
```

Restart Claude Code after making changes to reload plugins.

## Structure

```
claude-code-atelier/
├── .claude-plugin/
│   └── marketplace.json
├── plugins/
│   ├── atelier-spec/
│   │   ├── .claude-plugin/plugin.json
│   │   ├── skills/
│   │   │   ├── spec/
│   │   │   │   ├── SKILL.md
│   │   │   │   └── workflows/
│   │   │   ├── change/
│   │   │   │   ├── SKILL.md
│   │   │   │   └── workflows/
│   │   │   └── product/
│   │   │       ├── SKILL.md
│   │   │       └── workflows/
│   │   └── assets/templates/
│   ├── atelier-code/
│   │   ├── .claude-plugin/plugin.json
│   │   └── skills/
│   │       ├── atelier-review/SKILL.md
│   │       ├── atelier-commit/SKILL.md
│   │       ├── reviewing-code/SKILL.md
│   │       └── generating-commits/SKILL.md
│   ├── atelier-oracle/
│   │   ├── .claude-plugin/plugin.json
│   │   └── skills/
│   │       ├── atelier-debug/SKILL.md
│   │       ├── atelier-challenge/SKILL.md
│   │       └── atelier-thinkdeep/SKILL.md
│   └── atelier-typescript/
│       ├── .claude-plugin/plugin.json
│       └── skills/
│           ├── api-design/SKILL.md
│           ├── dynamodb-toolbox/SKILL.md
│           ├── drizzle-orm/SKILL.md
│           └── fastify-typebox/SKILL.md
├── LICENSE
└── README.md
```

## Architecture

The repository follows Daniel Miessler's PAI (Personal AI Infrastructure) pattern:

- **Commands** - User-invocable workflows triggered via `/command:name` syntax
- **Skills** - Auto-invoked contextual knowledge loaded when relevant
- **Agents** - Internal agent personas (@agent-name) for specialized thinking
- **Templates** - Document templates for specs, proposals, and deltas

### Plugin Structure

Each plugin lives in `plugins/atelier-{name}/` with:
- `.claude-plugin/plugin.json` - Plugin metadata and configuration
- `commands/*.md` - Command implementations (user-invoked via `/command`)
- `skills/*/SKILL.md` - Skill definitions (auto-invoked based on description)
- `agents/*.md` - Agent persona definitions (invoked via @agent-name)
- `assets/templates/*.md` - Document templates

## License

MIT
