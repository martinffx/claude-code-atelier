# Debug: Systematic Investigation & Bisect Debugging

## Step 1: Parse Debug Request & Classify Problem

<strategist>
@agent-oracle

Analyze the debug request: $ARGUMENTS

**Debug Context Extraction:**
- **Error type**: [runtime, logic, integration, performance]
- **Symptoms**: [specific error messages, unexpected behavior]
- **Environment**: [runtime versions, configuration, deployment context]
- **Reproduction**: [when, where, how the issue manifests]
- **Previous attempts**: [debugging steps already tried]

**Problem Classification:**
- **Category**: [runtime/logic/integration/performance]
- **Symptoms**: [specific manifestations]
- **Context**: [environmental factors]
- **Reproducibility**: [consistent/intermittent/rare]
</strategist>

## Step 2: Strategy Selection

<strategy_decision>
**Bisect Debugging Criteria:**
- Clear boundary between working and broken states
- Testable midpoint available
- Linear or temporal problem space
- Binary outcome testing possible

**Use Bisect Debugging if:**
- Git bisect: "When did this start breaking?" (commit history)
- Code bisect: "Which section contains the bug?" (code regions)
- Data bisect: "What input triggers the issue?" (test data)
- Time bisect: "When did the issue start?" (timeline)

**Use Systematic Investigation if:**
- Complex multi-factor issues
- No clear boundaries
- Requires comprehensive code analysis
- Integration or environmental problems
</strategy_decision>

## Step 3: Bisect Debugging (when applicable)

<strategist>
@agent-oracle

Use sequential thinking (mcp__sequential-thinking__sequentialthinking) to guide bisect debugging:

**Bisect Framework:**
- Initial thought: "Identify the boundary between working and broken states"
- Process: Test midpoint → Discard working half → Repeat recursively
- Continue: Until issue isolated to specific point
- Track: Confidence and hypothesis evolution

**Git Bisect Process:**
1. Boundary Identification: Last known good commit, first bad commit
2. Midpoint Testing: Test commit at midpoint between boundaries
3. Result Analysis: Working or broken?
4. Boundary Update: Discard working half, keep problematic half
5. Recursive Repeat: Continue until single problematic commit isolated

**Code Bisect Process:**
1. Region Identification: Working vs problematic code sections
2. Midpoint Testing: Comment out/test half the code
3. Result Analysis: Issue persists or resolved?
4. Region Update: Focus on problematic half
5. Recursive Repeat: Isolate specific problematic code section

**Data Bisect Process:**
1. Dataset Identification: Working vs problematic input data
2. Midpoint Testing: Test with half the dataset
3. Result Analysis: Issue occurs or not?
4. Dataset Update: Focus on problematic data subset
5. Recursive Repeat: Isolate specific problematic input
</strategist>

## Step 4: Systematic Investigation (when bisect not applicable)

<strategist>
@agent-oracle

Use sequential thinking for comprehensive debugging analysis:

**Thought 1**: Describe the issue and initial hypotheses
**Thought 2**: Examine relevant code and identify suspicious patterns
**Thought 3**: Collect evidence and track findings
**Thought 4**: Evolve hypotheses with confidence assessment
**Thought 5+**: Refine analysis with new insights

Continue until sufficient evidence gathered.

**Investigation Process:**
1. Problem Description: Detailed symptom analysis
2. Code Examination: Systematic file and method review
3. Evidence Collection: Track findings and suspicious patterns
4. Hypothesis Evolution: Develop and refine theories with confidence levels
5. Context Integration: Consider environmental and integration factors
</strategist>

## Step 5: Root Cause Analysis & Recommendations

**Root Cause Analysis:**
- **Isolated Issue**: [specific problem identified]
- **Contributing Factors**: [related issues or conditions]
- **Impact Assessment**: [scope and severity of problem]

**Solution Recommendations:**
- **Immediate Fix**: [specific code change or configuration]
- **Implementation Steps**: [how to apply the fix]
- **Testing Strategy**: [how to validate the solution]
- **Rollback Plan**: [if fix causes other issues]

**Prevention Strategies:**
- **Code Improvements**: [prevent similar future issues]
- **Testing Enhancements**: [catch issues earlier]
- **Process Changes**: [improve development workflow]
- **Monitoring Additions**: [detect issues proactively]

---

## Usage Examples

**Runtime Error Debugging:**
```
/oracle:debug "TypeError: 'NoneType' object has no attribute 'split' in parser.py"
```

**Git Bisect for Regression:**
```
/oracle:debug "Use git bisect to find when the authentication started failing"
```

**Performance Issue Investigation:**
```
/oracle:debug "App is consuming excessive memory during bulk operations"
```

## Debugging Categories

**Runtime Errors:** Exceptions, null references, type errors, memory leaks
**Logic Errors:** Algorithm bugs, boundary conditions, state issues, race conditions
**Integration Issues:** API failures, database problems, third-party services, configuration
**Performance Problems:** Slow responses, memory spikes, CPU bottlenecks, I/O issues

## Bisect Debugging Types

**Git Bisect:** `git bisect start → git bisect bad HEAD → git bisect good v1.0.0`
**Code Bisect:** Comment out half code → test → narrow down recursively
**Data Bisect:** Test with half dataset → identify problematic subset → repeat
