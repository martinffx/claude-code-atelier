# Atelier Oracle

Deep thinking and debugging workflows using sequential reasoning for complex problem-solving.

## Skills

| Skill | Type | Description |
|-------|------|-------------|
| oracle | User-invocable | Deep thinking and debugging with sequential reasoning |

## Workflows

```bash
/zen debug <error>         # Systematic debugging with bisect methodology
/zen challenge <topic>     # Challenge an approach with critical thinking
/zen thinkdeep <question>  # Extended reasoning analysis
```

## When to Use

| Scenario | Workflow |
|----------|----------|
| "Why is this failing?" | /zen debug |
| "Is this the right approach?" | /zen challenge |
| "Help me think through this" | /zen thinkdeep |
| "Find when this broke" | /zen debug (bisect) |
| "Are we over-engineering?" | /zen challenge |
| "Compare these options" | /zen thinkdeep |

## Debug Strategies

- **Git Bisect**: Find when something broke in commit history
- **Code Bisect**: Isolate buggy code by commenting out halves
- **Data Bisect**: Find problematic input by testing subsets
- **Systematic**: Multi-factor investigation when no clear boundaries

## Installation

```bash
claude --plugin-dir /path/to/claude-code-atelier/plugins/atelier-oracle
```

## License

MIT
