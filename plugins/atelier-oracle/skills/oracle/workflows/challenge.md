# Challenge: Critical Thinking Prompt

## Step 1: Parse Challenge Request

<strategist>
@agent-oracle

Analyze the challenge request: $ARGUMENTS

**Challenge Extraction:**
- **Core concern**: Extract the main doubt or question
- **Target approach**: Identify what is being challenged
- **Context**: Relevant background from current session
- **Specific aspects**: Particular elements to question

**Challenge Summary:**
You're challenging: [identified approach]
Because: [extracted concern]
In context of: [session context]
</strategist>

## Step 2: Set Up Critical Thinking Framework

<framework>
**What to Question:**
- **Underlying assumptions**: What beliefs support this approach?
- **Evidence base**: What data or experience validates it?
- **Context fit**: How well does it work in your specific situation?
- **Alternatives considered**: What other options were explored?
- **Risk factors**: What could go wrong with this approach?

**Critical Thinking Prompts:**
- Is this approach solving the right problem?
- Are the underlying assumptions still valid?
- What evidence contradicts this direction?
- How does this fit with your constraints and goals?
- What are the opportunity costs?
</framework>

## Step 3: Sequential Thinking Analysis

<strategist>
@agent-oracle

Use sequential thinking (mcp__sequential-thinking__sequentialthinking) to analyze this challenge:

**Thought 1**: Question the fundamental assumptions
**Thought 2**: Examine contradictory evidence
**Thought 3**: Explore alternative approaches
**Thought 4**: Assess context-specific fit
**Thought 5**: Evaluate risks and trade-offs
**Thought 6**: Synthesize findings into recommendation

Build systematically through evidence, alternatives, and risks.
Continue until you reach a clear conclusion.
</strategist>

## Step 4: Critical Evaluation Output

**Self-Critique Questions:**
- Does the analysis address the user's specific context?
- Are the recommendations practical and implementable?
- Have we considered the most important constraints?
- Are there any blind spots or missing perspectives?

**Final Synthesis:**
- **Assumption validity**: Are the underlying assumptions sound?
- **Evidence assessment**: Does evidence support or contradict?
- **Alternative recommendation**: If current approach is problematic, what instead?
- **Risk mitigation**: How to address identified concerns?

---

## Usage Examples

**Challenge Technical Decisions:**
```
/oracle challenge "Do we really need a microservices architecture for this simple app?"
```
→ Use sequential thinking to question complexity assumptions

**Challenge Implementation Approach:**
```
/oracle challenge "I think this caching strategy will actually slow things down"
```
→ Use sequential thinking to evaluate performance assumptions

**Challenge Requirements:**
```
/oracle challenge "Are we solving the right problem with this feature?"
```
→ Use sequential thinking to validate problem-solution fit

**Challenge Architectural Patterns:**
```
/oracle challenge "Should we really use event sourcing for this use case?"
```
→ Use sequential thinking to assess pattern appropriateness

## When to Use Challenge

**Before Major Decisions:**
- Architecture choices
- Technology stack decisions
- Design pattern selection
- Implementation approach

**When Something Feels Off:**
- "This seems overly complex"
- "I'm not sure this solves the real problem"
- "This approach feels wrong"
- "Are we over-engineering this?"

**To Prevent Automatic Agreement:**
- When you want genuine critical evaluation
- When you need to challenge conventional wisdom
- When you want to test your own assumptions

## Challenge vs ThinkDeep

**Use /oracle challenge**: Question assumptions, test validity, assess risks, prevent automatic agreement
**Use /oracle thinkdeep**: Deep exploration, comprehensive analysis, alternative discovery, complex decisions

**Key distinction**: Challenge = critical evaluation, ThinkDeep = deep exploration

## Expected Outcomes

After using this command:
- Clear identification of flawed assumptions
- Honest assessment of approach validity
- Recognition of risks and trade-offs
- Alternative solutions if current approach is problematic
- Confidence in decisions or clear direction for changes
