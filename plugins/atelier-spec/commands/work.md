# Implementation: $ARGUMENTS

Optional format: `<feature>` or empty (any ready task)

## Step 1: Check Beads and Find Ready Task

@context check Beads installation and find next ready task.

Check Beads:
```bash
bd --version
```
- If command fails → ERROR: "Beads not found. Install with: npm install -g @beads/bd, then run: bd init"

Find ready task:

**If feature specified:**
```bash
bd ready --label $ARGUMENTS --json
```

**If no arguments:**
```bash
bd ready --json
```

Parse result:
- If no ready tasks → REPORT: "No ready tasks. Check status: /spec:status [feature]"
- If ready tasks found → Select first task

Mark task in progress:
```bash
bd update <task-id> --status in_progress
```

## Step 2: Load Implementation Context

@coder load all necessary context for implementation.

Read specifications:

**If feature-only task:**
- `docs/spec/<feature>/spec.md` → requirements and design

**If change task:**
- `docs/spec/<feature>/spec.md` → current state
- `docs/changes/<feature>/<change>/delta.md` → changes to implement

Read standards:
- `docs/standards/coding.md` → implementation patterns and testing
- `docs/standards/tech.md` → layered architecture patterns

Extract from spec/delta:
- Entity definitions and methods
- Service operations
- Repository methods
- API endpoints
- Database schema
- Events

## Step 3: Implement Using Stub→Test→Fix Pattern

@coder implement task following layer boundary testing approach.

### Layer Boundary Testing Pattern

Test at boundaries, not every method:
- **Router**: HTTP request → mock Service → HTTP response
- **Service**: Entity in → mock Repository → Entity out
- **Repository**: Entity → real database → Entity
- **Client**: Request → MSW/VCR mock → Response

### Implementation Pattern: Stub→Test→Fix

#### STUB Phase
Create method signatures that throw NotImplementedError:

```typescript
// Example
async createUser(entity: UserEntity): Promise<UserEntity> {
  throw new Error('Not implemented');
}
```

```python
# Example
def create_user(self, entity: UserEntity) -> UserEntity:
    raise NotImplementedError("create_user not implemented")
```

#### TEST Phase
Write tests for layer boundary behavior:

**Router tests:**
```typescript
// Test real HTTP → mocked service
it('POST /users creates user', async () => {
  const mockService = { createUser: jest.fn().mockResolvedValue(userEntity) };
  const response = await app.inject({
    method: 'POST',
    url: '/users',
    payload: { email: 'test@example.com', name: 'Test' }
  });
  expect(response.statusCode).toBe(201);
  expect(mockService.createUser).toHaveBeenCalled();
});
```

**Service tests:**
```typescript
// Test real entity → mocked repository
it('createUser validates and persists', async () => {
  const mockRepo = { create: jest.fn(), findByEmail: jest.fn() };
  const service = new UserService(mockRepo);
  const entity = UserEntity.fromRequest({ email: 'test@example.com', name: 'Test' });
  await service.createUser(entity);
  expect(mockRepo.findByEmail).toHaveBeenCalledWith('test@example.com');
  expect(mockRepo.create).toHaveBeenCalled();
});
```

**Repository tests:**
```typescript
// Test real entity → real database (Docker)
it('create persists to database', async () => {
  const testDb = await TestDatabase.getInstance();
  const repo = new UserRepository(testDb.db);
  const entity = new UserEntity(undefined, 'test@example.com', 'Test');
  const result = await repo.create(entity.toRecord());
  expect(result.id).toBeDefined();
  expect(result.email).toBe('test@example.com');
});
```

#### FIX Phase
Implement methods to pass tests:

Follow dependency order: **Entity → Repository → Service → Router**

```typescript
// Example service implementation
async createUser(entity: UserEntity): Promise<UserEntity> {
  const validation = entity.validate();
  if (!validation.isValid) {
    throw new ValidationError(validation.errors.join(', '));
  }

  const existing = await this.repository.findByEmail(entity.email);
  if (existing) {
    throw new BusinessRuleError('Email already exists');
  }

  const record = entity.toRecord();
  const created = await this.repository.create(record);
  return UserEntity.fromRecord(created);
}
```

### Quality Checks

Before marking complete:
- [ ] All stubs replaced with implementations
- [ ] All tests passing
- [ ] Layer boundaries respected
- [ ] Proper error handling
- [ ] Types/interfaces defined
- [ ] No NotImplementedError remaining

## Step 4: Handle Discovered Work

During implementation, if new tasks are discovered:

**Create new Beads:**
```bash
bd create "Handle <edge case>" -t task -p 2 -l <feature>
bd dep add <new-task-id> <current-task-id> --type discovered-from
```

Examples of discovered work:
- Edge cases not in spec
- Additional validation needed
- Performance optimization required
- Missing error handling
- Integration issues

## Step 5: Mark Task Complete

@coder verify all quality checks pass and close task.

Run tests:
```bash
npm test # or appropriate test command
```

Verify all passing, then close task:
```bash
bd close <task-id> --reason "Implemented <component> with layer boundary tests"
```

Check for next ready task:
```bash
bd ready --label <feature> --json
```

## Task Complete

Completed: [task description]
- Implementation: [files modified]
- Tests: [test files created/updated]
- Coverage: [percentage if available]

Next ready task: [task-id and description] or "None - feature complete"

**Next steps:**

1. Continue next task: `/spec:work [feature]`
2. Check feature progress: `/spec:status [feature]`
3. Complete change: `/spec:complete <feature> <change>` (if working on a change)
