# Oracle

Deep thinking and debugging workflows using sequential reasoning for complex problem-solving.

## Skills

| Skill | Type | Description |
|-------|------|-------------|
| atelier-debug | User-invocable | Systematic debugging with bisect methodology |
| atelier-challenge | User-invocable | Challenge approaches with critical thinking |
| atelier-thinkdeep | User-invocable | Extended reasoning analysis |

## Usage

```bash
/atelier-debug <error>         # Systematic debugging with bisect methodology
/atelier-challenge <topic>     # Challenge an approach with critical thinking
/atelier-thinkdeep <question>  # Extended reasoning analysis
```

## When to Use

| Scenario | Skill |
|----------|-------|
| "Why is this failing?" | /atelier-debug |
| "Is this the right approach?" | /atelier-challenge |
| "Help me think through this" | /atelier-thinkdeep |
| "Find when this broke" | /atelier-debug (bisect) |
| "Are we over-engineering?" | /atelier-challenge |
| "Compare these options" | /atelier-thinkdeep |

## Debug Strategies

- **Git Bisect**: Find when something broke in commit history
- **Code Bisect**: Isolate buggy code by commenting out halves
- **Data Bisect**: Find problematic input by testing subsets
- **Systematic**: Multi-factor investigation when no clear boundaries

## Shared Agents

For enhanced workflows, install the **spec** plugin which provides shared agents:

| Agent | Purpose |
|-------|---------|
| atelier-architect | Technical design decisions |
| atelier-oracle | Requirements and strategic thinking |
| atelier-clerk | Fast utility tasks |

## Installation

```bash
/plugin marketplace add martinffx/claude-code-atelier
/plugin install oracle@atelier
/plugin install spec@atelier  # Recommended: provides shared agents
```

## License

MIT
