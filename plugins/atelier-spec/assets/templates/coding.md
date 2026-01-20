# Coding Standards

## TDD Workflow

Follow the **Stub → Test → Implement → Refactor** pattern:

1. **Stub** - Create minimal interface/function signature
2. **Test** - Write failing test for the behavior
3. **Implement** - Make the test pass
4. **Refactor** - Clean up without changing behavior

## Testing Requirements

- **Test at layer boundaries** - Focus on component interfaces, not every method
- **Unit tests** - Test business logic in isolation (services, entities)
- **Integration tests** - Test layer interactions (router → service → repository)
- **Use test doubles** - Stub dependencies below the layer being tested

## Naming Conventions

### Files
- Entities: `<name>.entity.ts`
- Services: `<name>.service.ts`
- Repositories: `<name>.repository.ts`
- Routers: `<name>.router.ts`
- Tests: `<name>.test.ts` (co-located with implementation)

### Functions and Methods
- Use descriptive verb-noun pairs: `createUser`, `findById`, `validateEmail`
- Predicates use `is`, `has`, `can`: `isValid`, `hasPermission`, `canDelete`
- Async functions clearly indicate async nature: `fetchUser`, `saveData`

### Variables
- Use meaningful names that reveal intent
- Boolean variables use `is`, `has`, `should`: `isActive`, `hasError`, `shouldRetry`
- Collections use plural: `users`, `items`, `results`

## Error Handling

### Standard Error Pattern
```typescript
class DomainError extends Error {
  constructor(
    message: string,
    public code: string,
    public statusCode: number = 500
  ) {
    super(message);
    this.name = this.constructor.name;
  }
}
```

### Error Boundaries
- **Entities** - Throw validation errors
- **Repositories** - Throw data access errors
- **Services** - Transform domain errors, add context
- **Routers** - Catch errors, return HTTP responses

### Error Handling Rules
- Never swallow errors silently
- Log errors with context before re-throwing
- Return errors at API boundaries (don't throw across boundaries)
- Use specific error types for different failure modes

## Code Organization

### Layer Responsibilities
- **Entities** - Data transformations, validation, business invariants
- **Repositories** - CRUD operations, query building, data mapping
- **Services** - Business logic orchestration, transaction boundaries
- **Routers** - HTTP request/response handling, input parsing, auth checks

### Dependency Rules
- Each layer depends only on layers below
- No circular dependencies between modules
- Use dependency injection for external dependencies

## Comments and Documentation

- **When to comment:**
  - Complex algorithms that aren't self-evident
  - Business rules that require context
  - Public APIs (JSDoc/docstrings)
  - Workarounds for known issues

- **When NOT to comment:**
  - Simple, self-explanatory code
  - What the code does (name it better instead)
  - Changes you made (git history tracks this)

## Code Review Checklist

- [ ] Tests pass and cover new code
- [ ] No console.log or debug code
- [ ] Error handling in place
- [ ] Naming follows conventions
- [ ] No hardcoded values (use config)
- [ ] Layer boundaries respected
- [ ] No obvious performance issues

---

**Note:** These are baseline standards. Customize for your stack and team preferences.
