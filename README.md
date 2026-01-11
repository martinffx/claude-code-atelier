# Claude Code Atelier

A software development atelier for Claude Code - spec-driven development, code quality, deep thinking, and TypeScript patterns.

## Plugins

| Plugin | Description | Skills |
|--------|-------------|--------|
| **spec** | Core SDD workflows for feature specifications and product management | spec, change, product |
| **code** | Code quality workflows for reviews, commits, and validation | code, reviewing-code, generating-commits |
| **oracle** | Deep thinking and debugging with sequential reasoning | oracle |
| **typescript** | TypeScript ecosystem patterns (auto-invoked) | api-design, dynamodb-toolbox, drizzle-orm, fastify-typebox |

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

## Commands

### spec (Core SDD)

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

### code

```bash
/code review [branch]          # Review code changes as senior engineer
/code commit [message]         # Create well-crafted conventional commit
```

### oracle

```bash
/oracle debug <error>          # Systematic debugging with bisect
/oracle challenge <topic>      # Challenge approach with critical thinking
/oracle thinkdeep <question>   # Extended reasoning analysis
```

### typescript

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
