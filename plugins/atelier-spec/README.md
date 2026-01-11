# Spec

Spec-Driven Development workflows for feature specifications, change management, and product documentation.

## Skills

| Skill | Type | Description |
|-------|------|-------------|
| atelier-spec | User-invocable | Feature specification lifecycle management |
| atelier-change | User-invocable | Brownfield change management with delta tracking |
| atelier-product | User-invocable | Product documentation and roadmap management |

## Usage

### Spec (Feature Specifications)

```bash
/atelier-spec create <feature>   # Create new feature specification
/atelier-spec work <feature>     # Implement next ready task
/atelier-spec status             # Track feature progress via Beads
/atelier-spec sync <feature>     # Update spec from code changes
```

### Change (Brownfield Development)

```bash
/atelier-change propose <feature>  # Propose changes to existing feature
/atelier-change complete <feature> # Merge delta into spec and close epic
```

### Product (Documentation)

```bash
/atelier-product init       # Initialize project documentation
/atelier-product progress   # Track product status
/atelier-product roadmap    # Update roadmap priorities
/atelier-product update     # Update methodology docs
```

## Prerequisites

- Beads CLI for task tracking: `npm install -g @beads/bd && bd init`
- Project structure: `docs/product/`, `docs/spec/`, `docs/standards/`

## Installation

```bash
/plugin marketplace add martinffx/claude-code-atelier
/plugin install spec@atelier
```

## License

MIT
