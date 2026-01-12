# Oracle

Deep thinking and debugging workflows using sequential reasoning for complex problem-solving.

## Commands

| Command | Description |
|---------|-------------|
| /oracle:debug | Systematic debugging with bisect methodology |

## Skills (auto-invoked)

| Skill | Description |
|-------|-------------|
| atelier-challenge | Challenge approaches with critical thinking |
| atelier-thinkdeep | Extended reasoning analysis |

## Usage

```bash
/oracle:debug <error>  # Systematic debugging with bisect methodology
```

## When to Use

| Scenario | Command |
|----------|---------|
| "Why is this failing?" | /oracle:debug |
| "Find when this broke" | /oracle:debug (bisect) |

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
