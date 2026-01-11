---
name: reviewing-code
description: Reviewing code changes like a senior engineer. Use when doing code reviews, checking PR quality, auditing changes before commit, or providing feedback on implementations.
user-invocable: false
---

# Code Review Patterns

Review code with precision. Flag real issues, skip noise.

## Severity Levels

### 🔴 Critical (Block merge)
- Security vulnerabilities (injection, auth bypass, secrets exposure)
- Data loss or corruption risks
- Breaking changes to public APIs
- Race conditions with data integrity impact
- Unhandled errors that crash the system

### 🟡 Important (Should fix)
- Bugs that affect functionality
- Performance issues (N+1 queries, unbounded loops, memory leaks)
- Missing error handling for likely failure cases
- Violation of established patterns/architecture
- Missing input validation on external data
- Hardcoded values that should be configurable

### 🟢 Minor (Consider fixing)
- Naming improvements for clarity
- Code duplication that could be extracted
- Missing edge case tests
- Documentation gaps
- Style inconsistencies not caught by linter

### ⚪ Skip (Don't mention)
- Issues a linter/formatter would catch
- Personal style preferences not in guidelines
- Pre-existing issues not introduced by this change
- Nitpicks that don't improve the code meaningfully

## Review Checklist

**Correctness**
- Does the code do what it claims?
- Are edge cases handled?
- Are error conditions caught and handled appropriately?

**Security**
- User input sanitized before use?
- Auth/authz checks in place?
- Secrets kept out of code?
- SQL/command injection prevented?

**Performance**
- Database queries efficient? (indexes, N+1)
- Appropriate caching?
- Unbounded operations paginated?
- Expensive operations async where appropriate?

**Maintainability**
- Clear naming and structure?
- Appropriate abstractions?
- Tests cover the important paths?
- Would a new team member understand this?

**Architecture**
- Follows established patterns?
- Correct layer boundaries?
- Dependencies flow in the right direction?
- No circular dependencies introduced?

## Giving Feedback

### Format
```
🔴 path/to/file.ts:42 - Brief issue description
   → Suggested fix or approach
```

### Good Feedback
- Specific: Points to exact location and problem
- Actionable: Suggests a concrete fix
- Educational: Explains why it matters
- Proportional: Severity matches actual impact

### Avoid
- Vague complaints ("this could be better")
- Rewrites without explanation
- Blocking on style preferences
- Piling on - one comment per issue type is enough

## Confidence Filter

Before reporting an issue, ask:
1. Is this a real problem or a preference?
2. Would a senior engineer call this out?
3. Is this introduced by this change (not pre-existing)?
4. Does it violate explicit guidelines or just implicit taste?

Only report issues where you're 80%+ confident they matter.

## Review Output Structure

```
**Summary:** One-line assessment of the change

**Issues:**
🔴 Critical issues (if any)
🟡 Important issues (if any)
🟢 Minor suggestions (limit to 2-3)

**Good:** What the code does well (always include something)
```

## Anti-patterns to Watch

| Pattern | Problem | Look For |
|---------|---------|----------|
| God object | Too many responsibilities | Class with 10+ methods, mixed concerns |
| Primitive obsession | Stringly-typed data | IDs as strings, status as raw strings |
| Deep nesting | Hard to follow logic | 4+ levels of indentation |
| Magic values | Unclear intent | Numbers/strings without constants |
| Silent failures | Bugs hide | Empty catch blocks, ignored return values |
| Temporal coupling | Order-dependent calls | Init must be called before use |
| Feature envy | Wrong home for logic | Method uses more of another class |

## Positive Signals

Also call out good patterns:
- Clean separation of concerns
- Good test coverage on critical paths
- Defensive error handling
- Clear naming that documents intent
- Appropriate use of types
- Thoughtful API design
