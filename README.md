# Claude Code Atelier

A software development atelier for Claude Code - spec-driven development, code quality, deep thinking, and TypeScript patterns.

## Plugins

| Plugin | Description | Type |
|--------|-------------|------|
| **spec** | Feature specification workflows (6 commands + 2 skills) | Commands + Skills |
| **code** | Code quality workflows | Skills |
| **oracle** | Deep thinking and debugging | Skills |
| **typescript** | TypeScript ecosystem patterns (auto-invoked) | Skills |

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

## Commands & Skills

### spec (Feature Specifications)

**Commands:**
```bash
/spec:create <feature>             # Create new feature specification (auto-init)
/spec:propose <feature> <change>   # Propose changes to existing feature
/spec:sync <feature>               # Update spec from code changes
/spec:work <feature>               # Implement next ready task
/spec:complete <feature> <change>  # Complete changes and merge delta
/spec:status                       # Track feature progress
```

**Skills (auto-invoked):**
- `project-structure` - Project structure patterns and initialization
- `methodology` - SDD principles, TDD workflows, architecture patterns

### code

```bash
/code:review                # Review code changes as senior engineer
/code:commit [message]      # Create well-crafted conventional commit
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
