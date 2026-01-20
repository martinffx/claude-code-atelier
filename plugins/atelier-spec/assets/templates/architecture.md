# Architecture Standards

## Layered Architecture Pattern

This project follows a strict **bottom-up dependency flow**:

```
Router → Service → Repository → Entity → Database
```

### Layer Responsibilities

#### Entity Layer
- Domain models with typed fields
- Data transformations: `fromRequest()`, `toRecord()`, `toResponse()`
- Validation: `validate()` method that throws on invalid data
- Business invariants (rules that must always be true)

**Example:**
```typescript
class User {
  constructor(
    public id: string,
    public email: string,
    public name: string
  ) {}

  static fromRequest(data: unknown): User { /* parse + validate */ }
  toRecord(): UserRecord { /* convert to DB format */ }
  toResponse(): UserResponse { /* convert to API format */ }
  validate(): void { /* throw if invalid */ }
}
```

#### Repository Layer
- CRUD operations for entities
- Query building and execution
- Data mapping (database records ↔ entities)
- Transaction management

**Example:**
```typescript
interface UserRepository {
  findById(id: string): Promise<User | null>
  save(user: User): Promise<void>
  delete(id: string): Promise<void>
}
```

#### Service Layer
- Business logic orchestration
- Multi-entity operations
- Transaction boundaries
- Domain event publishing

**Example:**
```typescript
class UserService {
  constructor(private userRepo: UserRepository) {}

  async createUser(data: unknown): Promise<User> {
    const user = User.fromRequest(data);
    user.validate();
    await this.userRepo.save(user);
    return user;
  }
}
```

#### Router Layer
- HTTP request/response handling
- Input parsing and output formatting
- Authentication/authorization checks
- Route definitions

**Example:**
```typescript
router.post('/users', async (req, res) => {
  const user = await userService.createUser(req.body);
  res.status(201).json(user.toResponse());
});
```

## Dependency Injection

### Constructor Injection Pattern
```typescript
class Service {
  constructor(
    private repository: Repository,
    private logger: Logger
  ) {}
}
```

### Benefits
- Testability (inject mocks/stubs)
- Flexibility (swap implementations)
- Explicit dependencies (visible in constructor)

## Alternative Patterns

### Event-Driven Pattern

When async communication is needed:

```
Producer → Event Broker → Consumer → Service → Repository
```

**Use when:**
- Decoupling components
- Background processing
- Fan-out to multiple consumers

### External API Integration

```
Router → Service → Client → External API
```

**Use when:**
- Calling third-party services
- Integrating with external systems

## API Design Guidelines

### RESTful Conventions
- `GET /resources` - List resources
- `GET /resources/:id` - Get single resource
- `POST /resources` - Create resource
- `PUT /resources/:id` - Replace resource
- `PATCH /resources/:id` - Update resource
- `DELETE /resources/:id` - Delete resource

### Response Formats
- Success: Return entity or list of entities
- Error: Return `{ error: { code, message } }`
- 2xx for success, 4xx for client errors, 5xx for server errors

### Pagination
```json
{
  "data": [...],
  "pagination": {
    "page": 1,
    "pageSize": 20,
    "total": 100
  }
}
```

## Testing Strategy

### Layer Boundary Testing

Test at component boundaries, not every method:

```typescript
// Entity tests - validation and transformations
describe('User.validate', () => { /* ... */ });

// Repository tests - stub database, test queries
describe('UserRepository.findById', () => { /* ... */ });

// Service tests - stub repository, test business logic
describe('UserService.createUser', () => { /* ... */ });

// Router tests - stub service, test HTTP handling
describe('POST /users', () => { /* ... */ });
```

### Stub-Driven TDD

1. **Stub** - Create interface/function signature
2. **Test** - Write failing test
3. **Implement** - Make test pass
4. **Refactor** - Clean up

## Performance Considerations

- **N+1 queries** - Use eager loading or batch queries
- **Large payloads** - Paginate results, stream responses
- **Expensive operations** - Cache results, use background jobs
- **Blocking operations** - Use async/await, avoid synchronous I/O

## Security Boundaries

### Input Validation
- Validate at entity layer (always)
- Sanitize at router layer (for display)
- Never trust client input

### Authentication & Authorization
- Authenticate at router layer (middleware)
- Authorize business operations in service layer
- Use role-based or attribute-based access control

### Data Exposure
- Use `toResponse()` to control what's exposed
- Never return raw database records to clients
- Filter sensitive fields (passwords, tokens)

---

**Note:** Adapt these patterns to your specific stack and requirements. The principles (layering, dependency flow, testing) remain consistent across technologies.
