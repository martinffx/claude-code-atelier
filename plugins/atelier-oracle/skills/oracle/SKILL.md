---
name: oracle
description: Deep thinking and debugging workflows using sequential reasoning. Use for systematic debugging with bisect methodology, challenging approaches with critical thinking, or deep analysis of complex problems.
user-invocable: true
---

# Oracle - Deep Thinking & Debugging

Extended reasoning partner for complex problem-solving, critical evaluation, and systematic debugging.

## Workflows

| Workflow | When to Use |
|----------|-------------|
| [debug](workflows/debug.md) | Systematic debugging with bisect methodology |
| [challenge](workflows/challenge.md) | Challenge an approach or validate ideas critically |
| [thinkdeep](workflows/thinkdeep.md) | Extended analysis using sequential thinking |

## Routing

Analyze the request and load the appropriate workflow:
- Debugging issues, finding root causes, bisect debugging → Read `workflows/debug.md`
- Challenging decisions, questioning assumptions, critical evaluation → Read `workflows/challenge.md`
- Deep analysis, complex decisions, extended reasoning → Read `workflows/thinkdeep.md`

## Quick Reference

### Debug Strategies
- **Git Bisect**: Find when something broke in commit history
- **Code Bisect**: Isolate buggy code by commenting out halves
- **Data Bisect**: Find problematic input by testing subsets
- **Systematic**: Multi-factor investigation when no clear boundaries

### Challenge Prompts
- Is this approach solving the right problem?
- Are the underlying assumptions valid?
- What evidence contradicts this direction?
- What are the opportunity costs?

### ThinkDeep Structure
1. Context Understanding
2. Problem Decomposition
3. Assumption Challenge
4. Alternative Exploration
5. Edge Case Analysis
6. Risk Assessment
7. Solution Synthesis

### When to Use Which

| Scenario | Workflow |
|----------|----------|
| "Why is this failing?" | debug |
| "Is this the right approach?" | challenge |
| "Help me think through this" | thinkdeep |
| "Find when this broke" | debug (bisect) |
| "Are we over-engineering?" | challenge |
| "Compare these options" | thinkdeep |
