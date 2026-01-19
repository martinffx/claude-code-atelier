# TypeScript Ecosystem Plugin

Just-in-time expertise for TypeScript development. This plugin provides contextual knowledge that loads automatically when you work with specific technologies - no commands to remember, no manual activation required.

## How It Works

Unlike command-based plugins, atelier-typescript provides **skills** - contextual knowledge that Claude loads automatically based on what you're working on. When you discuss Drizzle schemas, the drizzle-orm patterns appear. When you design a REST endpoint, API conventions surface. The right expertise arrives at the right moment.

This is **ambient intelligence** for TypeScript development:

- Working with DynamoDB entities? Single-table design patterns load automatically
- Building Fastify routes? TypeBox validation patterns become available
- Setting up a monorepo? Turborepo and Biome configurations are ready
- Defining database schemas? Drizzle ORM best practices guide your work

## Skills Reference

| Skill | Auto-Invoked When You... | What You Get |
|-------|--------------------------|--------------|
| **dynamodb-toolbox** | Create DynamoDB entities, design key patterns, write queries, implement pagination, or work with single-table design | Entity definitions with linked keys, GSI strategies, query patterns, pagination tokens, transaction handling, type-safe repository patterns |
| **drizzle-orm** | Define database schemas, write SQL queries, set up relations, run migrations, or work with PostgreSQL/MySQL/SQLite/D1/Durable Objects | Schema definitions, type inference patterns, relational queries, entity/repository patterns, database-specific guides for PostgreSQL, SQLite, and Cloudflare |
| **fastify** | Create routes, handle HTTP requests, implement TypeBox validation, structure applications, or work with plugins | TypeBox schema patterns, RFC 7807 error responses, modular route registration, auth/permissions decorators, OpenAPI-ready route definitions |
| **api-design** | Design endpoints, structure error responses, implement pagination, plan versioning, or architect REST APIs | Resource naming conventions, cursor-based pagination, HTTP status code guidance, idempotency patterns, filtering/sorting conventions |
| **build-tools** | Set up package.json scripts, configure builds, run typechecking, set up tests, or orchestrate monorepo development | Bun package manager patterns, tsgo typechecking, Vitest configuration, Biome linting/formatting, Turborepo task orchestration, CI pipeline templates |

## Example Triggers

**Drizzle ORM** loads when you:
- "Define a users table with posts relation"
- "Write a query to find users by email"
- "Set up Drizzle migrations for PostgreSQL"
- "Create a repository pattern for my entities"

**DynamoDB Toolbox** loads when you:
- "Design a single-table schema for a todo app"
- "Create an entity with GSI for querying by status"
- "Implement cursor-based pagination for DynamoDB"
- "Handle conditional writes with optimistic locking"

**Fastify** loads when you:
- "Create a POST /users endpoint with validation"
- "Set up TypeBox schemas for request/response"
- "Implement role-based permissions on routes"
- "Structure a Fastify application with plugins"

**API Design** loads when you:
- "What status code should I return for duplicate email?"
- "How should I structure error responses?"
- "Design pagination for a large dataset"
- "Plan API versioning strategy"

**Build Tools** loads when you:
- "Set up Vitest for a new project"
- "Configure Biome for linting and formatting"
- "Create a Turborepo monorepo structure"
- "Write CI pipeline for TypeScript project"

## What This Plugin Does NOT Do

This plugin provides **contextual knowledge**, not commands:

- No `/slash:commands` to invoke
- No code generation workflows
- No project scaffolding

It enhances Claude's understanding when you work with these technologies. The patterns and conventions become part of how Claude reasons about your code.

## Coverage by Technology

### DynamoDB Toolbox
- Single-table design methodology
- Entity definitions with computed keys
- GSI design and access patterns
- Type-safe queries with pagination
- Transaction patterns
- Repository and entity layer patterns

### Drizzle ORM
- Schema definitions (PostgreSQL, MySQL, SQLite)
- Type inference from schemas
- Relational queries and joins
- Entity and repository patterns
- Cloudflare D1 and Durable Objects
- Migration strategies

### Fastify + TypeBox
- Route definitions with full schemas
- Request/response validation
- RFC 7807 error handling
- Plugin architecture
- Auth and permissions
- OpenAPI generation

### REST API Design
- Resource naming (plural nouns, hyphens)
- HTTP methods and status codes
- Cursor-based pagination
- Filtering and sorting conventions
- Versioning strategies
- Idempotency patterns

### Build Tools
- Bun (package manager, task runner, bundler)
- tsgo (fast typechecking)
- Vitest (testing with coverage)
- Biome (linting and formatting)
- Turborepo (monorepo orchestration)
- CI/CD pipeline patterns

## Installation

```bash
# Add the Atelier marketplace
/plugin marketplace add martinffx/claude-code-atelier

# Install the TypeScript plugin
/plugin install typescript@atelier
```

Or load directly during development:

```bash
claude --plugin-dir ./claude-code-atelier/plugins/atelier-typescript
```

## License

MIT
