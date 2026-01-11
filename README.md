# Claude Code Atelier

A software development atelier for Claude Code - spec-driven development, code quality, deep thinking, and TypeScript patterns.

## Plugins

| Plugin | Description | Skills |
|--------|-------------|--------|
| **atelier** | Core SDD workflows for feature specifications and product management | spec, change, product |
| **atelier-code** | Code quality workflows for reviews, commits, and validation | code, reviewing-code, generating-commits |
| **atelier-oracle** | Deep thinking and debugging with sequential reasoning | oracle |
| **atelier-typescript** | TypeScript ecosystem patterns (auto-invoked) | api-design, dynamodb-toolbox, drizzle-orm, fastify-typebox |

## Installation

### Full Marketplace

```bash
claude --plugin-dir /path/to/claude-code-atelier
```

### Individual Plugins

```bash
claude --plugin-dir /path/to/claude-code-atelier/plugins/atelier
claude --plugin-dir /path/to/claude-code-atelier/plugins/atelier-code
claude --plugin-dir /path/to/claude-code-atelier/plugins/atelier-oracle
claude --plugin-dir /path/to/claude-code-atelier/plugins/atelier-typescript
```

## Commands

### atelier (Core SDD)

```bash
/spec create <feature-name>    # Create new feature specification
/spec work <feature-name>      # Implement next ready task
/spec status                   # Track feature progress
/spec sync <feature-name>      # Update spec from code changes

/change propose <feature-name> # Propose changes to existing feature
/change complete <feature-name> # Merge delta and close epic

/product init                  # Initialize project documentation
/product progress              # Track product status
/product roadmap               # Update roadmap priorities
/product update                # Update methodology docs
```

### atelier-code

```bash
/code review [branch]          # Review code changes as senior engineer
/code commit [message]         # Create well-crafted conventional commit
```

### atelier-oracle

```bash
/oracle debug <error>          # Systematic debugging with bisect
/oracle challenge <topic>      # Challenge approach with critical thinking
/oracle thinkdeep <question>   # Extended reasoning analysis
```

### atelier-typescript

Model-invoked (no slash command). Automatically loads relevant patterns when working with:
- REST API design
- DynamoDB with dynamodb-toolbox v2
- Drizzle ORM for PostgreSQL/MySQL/SQLite
- Fastify with TypeBox

## Structure

```
claude-code-atelier/
├── .claude-plugin/
│   └── marketplace.json
├── plugins/
│   ├── atelier/
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
│   │       ├── code/
│   │       │   ├── SKILL.md
│   │       │   └── workflows/
│   │       ├── reviewing-code/SKILL.md
│   │       └── generating-commits/SKILL.md
│   ├── atelier-oracle/
│   │   ├── .claude-plugin/plugin.json
│   │   └── skills/oracle/
│   │       ├── SKILL.md
│   │       └── workflows/
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

Based on Daniel Miessler's PAI (Personal AI Infrastructure) pattern:

- **Skills** - Router files (SKILL.md) that direct to appropriate workflows or references
- **Workflows** - Step-by-step procedures invoked via `/skill workflow` syntax
- **References** - Pattern knowledge loaded contextually (not directly invoked)
- **Templates** - Document templates for specs, proposals, and deltas

## License

MIT
