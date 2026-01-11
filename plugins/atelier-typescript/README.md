# TypeScript

TypeScript ecosystem patterns for Fastify, DynamoDB, Drizzle ORM, and REST API design.

## Skills

All skills are model-invoked (automatically loaded when relevant context is detected).

| Skill | Description |
|-------|-------------|
| api-design | REST API design conventions and best practices |
| dynamodb-toolbox | DynamoDB single-table design using dynamodb-toolbox v2 |
| drizzle-orm | Type-safe SQL with Drizzle ORM |
| fastify-typebox | Building REST APIs with Fastify and TypeBox |

## Usage

No slash commands - patterns are automatically loaded when working with:

- REST API endpoint design
- DynamoDB table design and queries
- Drizzle ORM schema definitions and migrations
- Fastify route handlers with TypeBox validation

## Pattern Coverage

### API Design
- Resource naming conventions
- HTTP methods and status codes
- Error response structure
- Pagination (cursor and offset)
- Versioning strategies

### DynamoDB Toolbox
- Single-table design patterns
- Entity definitions
- GSI design
- Query patterns
- Pagination

### Drizzle ORM
- Schema definitions
- Query building
- Relations
- Migrations
- PostgreSQL/MySQL/SQLite support

### Fastify + TypeBox
- Route definitions
- Request/response schemas
- Type-safe validation
- Plugin architecture

## Installation

```bash
/plugin marketplace add martinffx/claude-code-atelier
/plugin install typescript@atelier
```

## License

MIT
