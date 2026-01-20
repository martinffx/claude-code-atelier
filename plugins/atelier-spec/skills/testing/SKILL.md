---
name: testing
description: Stub-Driven TDD and layer boundary testing. Use when writing tests, deciding what to test, or testing at component boundaries.
user-invocable: false
---

# Testing Skill

Stub-Driven Test-Driven Development and layer boundary testing for functional core and effectful edge architecture.

## Stub-Driven TDD

Test-Driven Development workflow optimized for the functional core / effectful edge pattern:

### Workflow Steps

```
1. Stub   → Create minimal interface/function signatures
2. Test   → Write tests against stubs
3. Implement → Make tests pass with real implementation
4. Refactor  → Improve code while keeping tests green
```

### Why Stub-Driven?

**Traditional TDD problem:** Can't write tests until implementation exists

**Stub-Driven solution:** Write interface/type signatures first, test against those, then implement

**Benefits:**
- Tests define behavior before implementation
- Interfaces emerge from use cases, not speculation
- Refactoring is safe (tests validate behavior)
- Fast feedback loop (stub → test → implement)

### Example Flow

**1. Stub:**
```typescript
// Define interface from use case
interface OrderRepository {
  save(order: OrderRecord): Promise<OrderRecord>;
  findById(id: string): Promise<OrderRecord | null>;
}

// Service uses stub
class OrderService {
  constructor(private repo: OrderRepository) {}

  async createOrder(request: CreateOrderRequest): Promise<Result<Order>> {
    // Implementation comes later
    throw new Error('Not implemented');
  }
}
```

**2. Test:**
```typescript
describe('OrderService.createOrder', () => {
  it('should save valid order', async () => {
    // Create stub implementation
    const mockRepo: OrderRepository = {
      save: vi.fn().mockResolvedValue({ id: '123', status: 'pending' }),
      findById: vi.fn()
    };

    const service = new OrderService(mockRepo);
    const result = await service.createOrder({
      customerId: 'C1',
      items: [{ productId: 'P1', quantity: 2 }]
    });

    expect(result.ok).toBe(true);
    expect(mockRepo.save).toHaveBeenCalledWith(
      expect.objectContaining({ customerId: 'C1' })
    );
  });
});
```

**3. Implement:**
```typescript
async createOrder(request: CreateOrderRequest): Promise<Result<Order>> {
  const order = Order.fromRequest(request);
  const validation = order.validate();
  if (!validation.ok) return validation;

  const saved = await this.repo.save(order.toRecord());
  return Ok(Order.fromRecord(saved));
}
```

**4. Refactor:**
```typescript
// Extract validation, improve error handling, etc.
// Tests ensure behavior unchanged
```

## Boundary Testing

Test at the boundaries between functional core and effectful edge, not internal implementation.

### Testing Boundaries

```
Test here ──────▼──────────────────▼────── Test here
          Effectful Edge    │    Functional Core
              (stub)        │       (unit test)
```

**Key Principle:** Test the contract between layers, not the implementation within layers.

### Where to Test

| Layer | Test Type | What to Test | How to Test |
|-------|-----------|--------------|-------------|
| Entity | Unit | Validation, transforms, business rules | Pure function tests |
| Service | Unit | Orchestration logic | Stub repositories |
| Router | Integration | Request/response handling | Real HTTP, stub service |
| Repository | Integration | Data access | Real database or test DB |
| Consumer | Integration | Event handling | Real events, stub service |
| Producer | Integration | Event publishing | Verify event format |
| Client | Integration | External API calls | Mock server or contract test |

### Boundary Examples

**Core Boundary (Entity):**
```typescript
describe('Order entity', () => {
  describe('validation', () => {
    it('rejects empty items', () => {
      const order = new Order('1', 'C1', [], 'pending', 0);
      const result = order.validate();
      expect(result.ok).toBe(false);
      expect(result.error).toContain('at least one item');
    });
  });

  describe('transformations', () => {
    it('converts request to entity', () => {
      const order = Order.fromRequest({
        customerId: 'C1',
        items: [{ productId: 'P1', quantity: 2, price: 10 }]
      });
      expect(order.total).toBe(20);
    });
  });
});
```

**Core Boundary (Service):**
```typescript
describe('OrderService', () => {
  it('creates order with valid data', async () => {
    // Stub the edge (repository)
    const mockRepo: OrderRepository = {
      save: vi.fn().mockResolvedValue({ id: '123' }),
      findById: vi.fn()
    };

    const service = new OrderService(mockRepo);
    const result = await service.createOrder({
      customerId: 'C1',
      items: [{ productId: 'P1', quantity: 2 }]
    });

    // Test orchestration logic
    expect(result.ok).toBe(true);
    expect(mockRepo.save).toHaveBeenCalled();
  });

  it('returns error for invalid order', async () => {
    const mockRepo: OrderRepository = {
      save: vi.fn(),
      findById: vi.fn()
    };

    const service = new OrderService(mockRepo);
    const result = await service.createOrder({
      customerId: 'C1',
      items: []  // Invalid: empty items
    });

    // Test validation propagation
    expect(result.ok).toBe(false);
    expect(mockRepo.save).not.toHaveBeenCalled();
  });
});
```

**Edge Boundary (Router):**
```typescript
describe('POST /orders', () => {
  it('returns 201 for valid request', async () => {
    // Stub the core (service)
    const mockService = {
      createOrder: vi.fn().mockResolvedValue(
        Ok(new Order('123', 'C1', [], 'pending', 0))
      )
    };

    const app = createApp(mockService);
    const response = await request(app)
      .post('/orders')
      .send({ customerId: 'C1', items: [{ productId: 'P1', quantity: 2 }] });

    expect(response.status).toBe(201);
    expect(response.body.id).toBe('123');
  });

  it('returns 400 for invalid request', async () => {
    const mockService = {
      createOrder: vi.fn().mockResolvedValue(Err('Invalid order'))
    };

    const app = createApp(mockService);
    const response = await request(app)
      .post('/orders')
      .send({ customerId: 'C1', items: [] });

    expect(response.status).toBe(400);
    expect(response.body.error).toBeDefined();
  });
});
```

**Edge Boundary (Repository):**
```typescript
describe('OrderRepository', () => {
  it('saves order to database', async () => {
    const repo = new OrderRepository(testDb);
    const record = {
      id: '123',
      customer_id: 'C1',
      items: JSON.stringify([]),
      status: 'pending',
      total: 0
    };

    const saved = await repo.save(record);

    expect(saved.id).toBe('123');

    // Verify in database
    const found = await testDb.orders.findOne({ id: '123' });
    expect(found).toBeDefined();
  });
});
```

## Core Testing

Testing the functional core (Entity + Service):

### Entity Tests

**Focus:** Pure function behavior

**Test:**
- Validation rules
- Business rules
- Data transformations
- Edge cases
- Invariants

**Don't Test:**
- Getters/setters
- Simple property assignment
- Framework code

**Example:**
```typescript
describe('Order entity', () => {
  describe('business rules', () => {
    it('cannot cancel shipped order', () => {
      const order = new Order('1', 'C1', [], 'shipped', 0);
      expect(order.canCancel()).toBe(false);
    });

    it('can cancel pending order', () => {
      const order = new Order('1', 'C1', [], 'pending', 0);
      expect(order.canCancel()).toBe(true);
    });
  });

  describe('total calculation', () => {
    it('sums item totals', () => {
      const order = Order.fromRequest({
        customerId: 'C1',
        items: [
          { productId: 'P1', quantity: 2, price: 10 },
          { productId: 'P2', quantity: 1, price: 15 }
        ]
      });
      expect(order.total).toBe(35);
    });
  });
});
```

### Service Tests

**Focus:** Orchestration logic with stubbed dependencies

**Test:**
- Business workflow execution
- Error propagation
- Repository interaction patterns
- Multiple entity coordination

**Don't Test:**
- Repository implementation
- External API behavior
- Database queries

**Stubbing Pattern:**
```typescript
describe('OrderService', () => {
  let service: OrderService;
  let mockOrderRepo: OrderRepository;
  let mockInventoryRepo: InventoryRepository;

  beforeEach(() => {
    // Create stubs
    mockOrderRepo = {
      save: vi.fn(),
      findById: vi.fn()
    };
    mockInventoryRepo = {
      checkAvailability: vi.fn(),
      reserve: vi.fn()
    };

    service = new OrderService(mockOrderRepo, mockInventoryRepo);
  });

  it('checks inventory before creating order', async () => {
    mockInventoryRepo.checkAvailability.mockResolvedValue({
      available: true
    });
    mockOrderRepo.save.mockResolvedValue({ id: '123' });

    await service.createOrder({
      customerId: 'C1',
      items: [{ productId: 'P1', quantity: 2 }]
    });

    expect(mockInventoryRepo.checkAvailability).toHaveBeenCalledWith(
      expect.arrayContaining([
        expect.objectContaining({ productId: 'P1', quantity: 2 })
      ])
    );
  });

  it('does not save order when inventory unavailable', async () => {
    mockInventoryRepo.checkAvailability.mockResolvedValue({
      available: false
    });

    const result = await service.createOrder({
      customerId: 'C1',
      items: [{ productId: 'P1', quantity: 100 }]
    });

    expect(result.ok).toBe(false);
    expect(mockOrderRepo.save).not.toHaveBeenCalled();
  });
});
```

## Edge Testing

Testing the effectful edge (Router, Repository, Consumer, Producer, Client):

### Integration Test Strategy

**Test real IO boundaries:**
- Real HTTP requests (Router)
- Real database (Repository)
- Real message queue (Consumer/Producer)
- Mock external APIs (Client)

**Use test fixtures:**
- Test database with seed data
- Test message queue
- Mock servers for external APIs

### Router Integration Tests

```typescript
describe('Order API', () => {
  let app: Express;
  let testDb: Database;

  beforeAll(async () => {
    testDb = await createTestDatabase();
    const repo = new OrderRepository(testDb);
    const service = new OrderService(repo);
    app = createApp(service);
  });

  afterAll(async () => {
    await testDb.close();
  });

  it('creates order end-to-end', async () => {
    const response = await request(app)
      .post('/orders')
      .send({
        customerId: 'C1',
        items: [{ productId: 'P1', quantity: 2 }]
      });

    expect(response.status).toBe(201);
    expect(response.body.id).toBeDefined();

    // Verify in database
    const order = await testDb.orders.findOne({ id: response.body.id });
    expect(order).toBeDefined();
  });
});
```

### Repository Integration Tests

```typescript
describe('OrderRepository', () => {
  let repo: OrderRepository;
  let testDb: Database;

  beforeEach(async () => {
    testDb = await createTestDatabase();
    repo = new OrderRepository(testDb);
  });

  afterEach(async () => {
    await testDb.close();
  });

  it('saves and retrieves order', async () => {
    const record = {
      id: '123',
      customer_id: 'C1',
      items: JSON.stringify([]),
      status: 'pending',
      total: 0
    };

    await repo.save(record);
    const found = await repo.findById('123');

    expect(found).toEqual(record);
  });
});
```

### Consumer Integration Tests

```typescript
describe('OrderConsumer', () => {
  it('handles OrderPlaced event', async () => {
    const mockService = {
      processOrder: vi.fn().mockResolvedValue(Ok({}))
    };

    const consumer = new OrderConsumer(mockService);
    const event = {
      type: 'OrderPlaced',
      data: { orderId: '123', customerId: 'C1' }
    };

    await consumer.handle(event);

    expect(mockService.processOrder).toHaveBeenCalledWith('123');
  });
});
```

## What NOT to Test

Avoid testing these to reduce maintenance burden:

### Internal Implementation Details
```typescript
// ❌ Don't test
class OrderService {
  private calculateTotal(items: OrderItem[]): number {
    return items.reduce((sum, item) => sum + item.price * item.quantity, 0);
  }
}

// Test the public method that uses it instead
```

### Framework Code
```typescript
// ❌ Don't test framework behavior
it('express parses JSON', async () => {
  const response = await request(app)
    .post('/orders')
    .send({ customerId: 'C1' });
  expect(typeof response.body).toBe('object');
});
```

### Trivial Getters/Setters
```typescript
// ❌ Don't test
class Order {
  get id(): string { return this._id; }
}

it('returns id', () => {
  const order = new Order('123', 'C1', [], 'pending', 0);
  expect(order.id).toBe('123');
});
```

### Third-Party Libraries
```typescript
// ❌ Don't test library behavior
it('lodash groupBy works', () => {
  const result = _.groupBy([1, 2, 3], x => x % 2);
  expect(result[0]).toEqual([2]);
});
```

### Simple Mappings
```typescript
// ❌ Don't test unless transformation has logic
toResponse(): OrderResponse {
  return {
    id: this.id,
    customerId: this.customerId,
    total: this.total
  };
}
```

**When to test mappings:**
- Includes calculations or transformations
- Has conditional logic
- Combines multiple sources
- Critical for API contract

## Test Matrix

Quick reference for testing each component:

| Component | Test Type | Stub/Real | Focus | Example |
|-----------|-----------|-----------|-------|---------|
| **Entity** | Unit | N/A (pure) | Validation, rules, transforms | `order.validate()` returns error for empty items |
| **Service** | Unit | Stub repos | Orchestration, error handling | Service checks inventory before saving order |
| **Router** | Integration | Real HTTP, stub/real service | Request parsing, status codes | POST /orders returns 201 with order ID |
| **Repository** | Integration | Real test DB | CRUD operations, queries | Save and retrieve order from database |
| **Consumer** | Integration | Real events, stub service | Event parsing, service calls | OrderPlaced event triggers order processing |
| **Producer** | Integration | Real queue | Event formatting, publishing | OrderCreated event published with correct schema |
| **Client** | Integration | Mock server | Request format, response handling | Payment API called with correct payload |

## Test Coverage Guidelines

**100% coverage is not the goal.** Test what matters:

### High Coverage (Critical)
- Entity validation and business rules
- Service orchestration logic
- Critical user journeys (integration tests)
- Data transformations with logic

### Medium Coverage (Important)
- Error handling paths
- Edge cases in business logic
- API contract validation

### Low Coverage (Optional)
- Simple getters/setters
- Framework boilerplate
- Trivial mappings
- Internal utilities

**Focus:** High confidence in correctness with minimal test maintenance.

## Testing → Implementation Flow

Testing strategy follows from architecture:

```
Architect Outputs           →    Testing Strategy
────────────────────────────────────────────────
Entity (pure functions)     →    Unit tests
Service (orchestration)     →    Unit tests with stubs
Router (HTTP)              →    Integration tests
Repository (database)       →    Integration tests with test DB
Component boundaries        →    Test boundaries between layers
```

Start testing at the bottom of the dependency chain:
1. Entity tests (pure, fast)
2. Service tests (stubbed, fast)
3. Integration tests (real IO, slower)

This order enables TDD: write tests before implementation, with each layer building on tested layers below.
