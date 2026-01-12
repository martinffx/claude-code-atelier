# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is the Claude Code Atelier - a marketplace of plugins for spec-driven development, code quality workflows, deep thinking, and TypeScript ecosystem patterns. The repository contains four distinct plugins that integrate with Claude Code via the plugin system.

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

The marketplace root contains `.claude-plugin/marketplace.json` defining the plugin collection.

### Four Plugins

1. **atelier-spec** - Spec-Driven Development workflows
   - Commands: create, propose, sync, work, complete, status
   - Skills: project-structure, methodology
   - Focus: Feature specifications with unified requirements + technical design

2. **atelier-code** - Code quality workflows
   - Commands: review, commit
   - Focus: Senior engineer reviews and conventional commits

3. **atelier-oracle** - Deep thinking and debugging
   - Commands: debug
   - Skills: atelier-challenge, atelier-thinkdeep
   - Focus: Sequential reasoning for complex problems

4. **atelier-typescript** - TypeScript ecosystem patterns
   - Skills: dynamodb-toolbox, drizzle-orm, fastify, api-design
   - Focus: Auto-invoked patterns for specific tech stacks

## Development Commands

### Local Plugin Development

```bash
# Load entire marketplace (all plugins)
claude --plugin-dir ./claude-code-atelier

# Load individual plugins
claude --plugin-dir ./claude-code-atelier/plugins/atelier-spec
claude --plugin-dir ./claude-code-atelier/plugins/atelier-code
claude --plugin-dir ./claude-code-atelier/plugins/atelier-oracle
claude --plugin-dir ./claude-code-atelier/plugins/atelier-typescript
```

**Important:** Restart Claude Code after making changes to plugin files to reload them.

### Installation from Marketplace

```bash
# Add marketplace
/plugin marketplace add martinffx/claude-code-atelier

# Install plugins
/plugin install spec@atelier
/plugin install code@atelier
/plugin install oracle@atelier
/plugin install typescript@atelier
```

## Key Design Patterns

### Spec-Driven Development (atelier-spec)

The spec plugin enforces a layered architecture pattern:

```
Router → Service → Repository → Entity → Database
```

**Core principles:**
- **Bottom-up dependency flow** - Each layer depends only on layers below
- **Domain entities** - Entity layer handles all data transformations (fromRequest, toRecord, toResponse, validate)
- **Stub-Driven TDD** - Stub → Test → Implement → Refactor workflow
- **Layer boundary testing** - Test at component boundaries, not every method
- **Unified specs** - Single spec.md contains both requirements and technical design

**Project structure:**
```
docs/
├── product/          # Product-level docs (product.md, roadmap.md)
├── spec/            # Feature specs (one directory per feature)
│   └── <feature>/
│       └── spec.md  # Unified requirements + technical design
└── standards/       # Technical standards (coding.md, architecture.md)
```

### Command Invocation Pattern

Commands use shell command substitution (`!` prefix) and agent invocation (`@agent-name`) patterns:

```markdown
# Execute shell commands
!`git status`

# Invoke agents
<architect>
@agent-architect

[Instructions for agent]
</architect>
```

### Agent Context Pattern

Commands can request context loading:

```markdown
<context>
@agent-clerk

Load project standards for review criteria:
- Read `docs/standards/coding.md`
- Read `docs/standards/architecture.md`
</context>
```

### Skill Auto-Invocation

Skills have YAML frontmatter defining when they're loaded:

```yaml
---
name: skill-name
description: When to use this skill (Claude reads this)
user-invocable: false  # or true if user can call directly
---
```

Skills are loaded based on description match to current context.

## Plugin File Conventions

### Command Files (commands/*.md)

- Named with action verb (create.md, review.md, commit.md)
- Accept arguments via `$ARGUMENTS` placeholder
- Use step-by-step structure (## Step 1, ## Step 2, etc.)
- Can invoke agents and execute shell commands

### Skill Files (skills/*/SKILL.md)

- Located in named subdirectories under skills/
- Must have YAML frontmatter with name, description, user-invocable
- Description determines when Claude auto-loads the skill
- Can include references/ subdirectory for additional context

### Agent Files (agents/*.md)

- Define personas with specialized thinking modes
- Invoked via `@agent-name` syntax within command context blocks
- Common agents: architect (design), oracle (reasoning), clerk (context)

### Template Files (assets/templates/*.md)

- Document templates for consistent format
- Used by commands to generate specs, proposals, deltas
- Examples: spec.md, proposal.md, delta.md

## Modifying Plugins

When editing plugins:

1. **Commands** - Modify workflow steps, add shell commands, adjust agent instructions
2. **Skills** - Update knowledge content, adjust description for better auto-invocation
3. **Agents** - Refine persona instructions and thinking patterns
4. **Templates** - Adjust document structure and required sections

After changes, restart Claude Code with `--plugin-dir` to reload.

## Testing Plugin Changes

1. Make changes to plugin files
2. Restart Claude Code: `claude --plugin-dir ./claude-code-atelier`
3. Invoke commands: `/command:name arguments`
4. Verify skill auto-invocation by checking context loaded
5. Check command execution flow and output

## Integration with Beads Task Tracker

The spec plugin integrates with Beads (`bd` CLI) for dependency-aware task management:

- `bd init` - Initialize Beads in project
- `bd create epic <name>` - Create epic for feature
- `bd create task <name>` - Add implementation task
- `bd ready` - Start next available task
- `bd close` - Complete current task
- `bd list` - Show all tasks

Commands like `/spec:create` and `/spec:propose` automatically create Beads epics with implementation tasks ordered by technical dependencies (Entity → Repository → Service → Router).
