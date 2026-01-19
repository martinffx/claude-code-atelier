# Oracle

**Deep, sequential reasoning for problems that deserve more than quick answers.**

Some problems need deeper analysis - complex bugs with hidden root causes, architectural decisions with cascading trade-offs, or situations where your initial intuition might be wrong. Oracle provides structured thinking workflows that help Claude reason through problems step by step, challenge assumptions, and explore alternatives systematically.

## When to Use Oracle vs Regular Claude

| Situation | Regular Claude | Oracle |
|-----------|----------------|--------|
| Simple bug with clear stack trace | Works well | Overkill |
| Bug that spans multiple systems | Misses connections | Traces dependencies systematically |
| Quick code review | Sufficient | Unnecessary |
| Architecture decision with trade-offs | Surface-level analysis | Evaluates alternatives deeply |
| "Something feels wrong" instinct | Tends to agree with you | Challenges assumptions explicitly |
| Performance issue with unclear cause | Guesses at solutions | Investigates systematically |

**Use Oracle when:**
- The root cause is not obvious
- You need to challenge your own assumptions
- Multiple approaches exist with non-trivial trade-offs
- The problem spans multiple layers or systems
- Previous debugging attempts have failed

## How It Works

Oracle uses the **MCP sequential-thinking server** to structure reasoning into explicit, numbered steps. This prevents jumping to conclusions and ensures thorough analysis.

```
Thought 1: Understand the problem and context
    |
Thought 2: Identify assumptions and question them
    |
Thought 3: Gather evidence (what supports/contradicts?)
    |
Thought 4: Generate alternatives
    |
Thought 5: Evaluate trade-offs
    |
Thought 6: Synthesize recommendation
    |
   ...
(continues until satisfied)
```

The sequential thinking process supports:
- **Revision** - Go back and refine earlier thoughts
- **Branching** - Explore alternative reasoning paths
- **Extension** - Add more thoughts when needed
- **Confidence tracking** - Assess certainty at each step

## Commands

| Command | Description |
|---------|-------------|
| `/oracle:debug` | Systematic debugging with bisect methodology |

## Skills (auto-invoked)

| Skill | When It Activates | Purpose |
|-------|-------------------|---------|
| `atelier-challenge` | Questioning assumptions, validating decisions | Critical evaluation that resists automatic agreement |
| `atelier-thinkdeep` | Complex decisions, architectural analysis | Extended exploration with alternative discovery |

### Challenge vs ThinkDeep

**Challenge** is for critical evaluation - "Should we really do this?"
- Questions underlying assumptions
- Tests validity of current approach
- Identifies risks and blind spots
- Prevents automatic agreement with flawed premises

**ThinkDeep** is for comprehensive exploration - "What are all our options?"
- Deep problem decomposition
- Alternative approach discovery
- Trade-off analysis across multiple dimensions
- Synthesis into actionable recommendations

## Usage

### Debugging Complex Issues

```bash
# Systematic debugging with sequential reasoning
/oracle:debug "TypeError occurs intermittently in production but not locally"

# Use git bisect to find regression
/oracle:debug "Find when authentication started failing using git bisect"

# Investigate performance degradation
/oracle:debug "API response times increased 3x after last deploy"
```

### Debug Strategies

Oracle selects the appropriate strategy based on the problem:

| Strategy | When Used | Process |
|----------|-----------|---------|
| **Git Bisect** | "When did this break?" | Binary search through commit history |
| **Code Bisect** | "Which section causes this?" | Comment out halves recursively |
| **Data Bisect** | "What input triggers this?" | Test with data subsets |
| **Systematic** | Complex multi-factor issues | Sequential hypothesis elimination |

### Auto-Invoked Analysis

The skills activate automatically when Claude detects relevant situations:

```
User: "I'm not sure if microservices is the right choice here"
      -> atelier-challenge activates
      -> Questions assumptions about scale, team size, complexity
      -> Evaluates monolith vs microservices trade-offs

User: "Help me think through our caching strategy"
      -> atelier-thinkdeep activates
      -> Explores cache invalidation approaches
      -> Analyzes consistency vs performance trade-offs
      -> Recommends approach with rationale
```

## Example: Sequential Debugging in Action

```
/oracle:debug "Users randomly logged out after deploy"

Thought 1: Problem Classification
- Symptom: Intermittent logout
- Timing: Post-deploy
- Pattern: Random users, not all
- Hypothesis: Session handling or token validation change

Thought 2: Evidence Gathering
- Review deploy diff for auth-related changes
- Check session storage configuration
- Examine token expiration logic
- Look for race conditions in middleware

Thought 3: Bisect Strategy Selection
- Clear "before/after" boundary (deploy)
- Testable with specific user sessions
- Selecting: Git bisect + systematic investigation

Thought 4: Root Cause Isolation
- Found: JWT secret rotation without grace period
- Old tokens invalid immediately
- Race condition during rolling deploy

Thought 5: Solution Synthesis
- Immediate: Add grace period for old secret
- Prevention: Token rotation with overlap window
- Monitoring: Alert on auth failure spike
```

## Shared Agents

For enhanced workflows, install the **spec** plugin which provides agents Oracle can leverage:

| Agent | Purpose |
|-------|---------|
| `atelier-architect` | Technical design decisions |
| `atelier-oracle` | Requirements and strategic thinking |
| `atelier-clerk` | Fast utility tasks |

## Installation

```bash
/plugin marketplace add martinffx/claude-code-atelier
/plugin install oracle@atelier
/plugin install spec@atelier  # Recommended: provides shared agents
```

## License

MIT
