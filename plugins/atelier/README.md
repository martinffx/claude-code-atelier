# Atelier

Spec-Driven Development workflows for feature specifications, change management, and product documentation.

## Skills

| Skill | Type | Description |
|-------|------|-------------|
| spec | User-invocable | Feature specification lifecycle management |
| change | User-invocable | Brownfield change management with delta tracking |
| product | User-invocable | Product documentation and roadmap management |

## Workflows

### Spec (Feature Specifications)

```bash
/spec create <feature-name>   # Create new feature specification
/spec work <feature-name>     # Implement next ready task
/spec status                  # Track feature progress via Beads
/spec sync <feature-name>     # Update spec from code changes
```

### Change (Brownfield Development)

```bash
/change propose <feature-name>  # Propose changes to existing feature
/change complete <feature-name> # Merge delta into spec and close epic
```

### Product (Documentation)

```bash
/product init       # Initialize project documentation
/product progress   # Track product status
/product roadmap    # Update roadmap priorities
/product update     # Update methodology docs
```

## Prerequisites

- Beads CLI for task tracking: `npm install -g @beads/bd && bd init`
- Project structure: `docs/product/`, `docs/spec/`, `docs/standards/`

## Installation

```bash
claude --plugin-dir /path/to/claude-code-atelier/plugins/atelier
```

## License

MIT
