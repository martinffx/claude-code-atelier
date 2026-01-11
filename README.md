# Claude Code Atelier

A software development atelier for Claude Code - spec-driven development, code quality, deep thinking, and TypeScript patterns.

## Plugins

| Plugin | Description | Skills |
|--------|-------------|--------|
| **spec** | Core SDD workflows for feature specifications and product management | atelier-spec, atelier-change, atelier-product |
| **code** | Code quality workflows for reviews and commits | atelier-review, atelier-commit |
| **oracle** | Deep thinking and debugging with sequential reasoning | atelier-debug, atelier-challenge, atelier-thinkdeep |
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

## Skills

### spec (Core SDD)

```bash
/atelier-spec create <feature>    # Create new feature specification
/atelier-spec work <feature>      # Implement next ready task
/atelier-spec status              # Track feature progress
/atelier-spec sync <feature>      # Update spec from code changes

/atelier-change propose <feature> # Propose changes to existing feature
/atelier-change complete <feature> # Merge delta and close epic

/atelier-product init             # Initialize project documentation
/atelier-product progress         # Track product status
/atelier-product roadmap          # Update roadmap priorities
/atelier-product update           # Update methodology docs
```

### code

```bash
/atelier-review                # Review code changes as senior engineer
/atelier-commit [message]      # Create well-crafted conventional commit
```

### oracle

```bash
/atelier-debug <error>         # Systematic debugging with bisect
/atelier-challenge <topic>     # Challenge approach with critical thinking
/atelier-thinkdeep <question>  # Extended reasoning analysis
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

Based on Daniel Miessler's PAI (Personal AI Infrastructure) pattern:

- **Skills** - Router files (SKILL.md) that direct to appropriate workflows or references
- **Workflows** - Step-by-step procedures invoked via `/skill workflow` syntax
- **References** - Pattern knowledge loaded contextually (not directly invoked)
- **Templates** - Document templates for specs, proposals, and deltas

## License

MIT
