---
name: architect
description: DDD and hexagonal architecture with functional core pattern. Use when designing features, modeling domains, breaking down tasks, or understanding component responsibilities.
user-invocable: false
---

# Architect Skill

Domain-Driven Design and hexagonal architecture with functional core pattern for feature design.

## Architecture Model

Unified view of functional core and effectful edge:

```
          Effectful Edge (IO)              Functional Core (Pure)
┌─────────────────────────────────┐    ┌──────────────────────────┐
│  Router    → request parsing    │    │  Service  → orchestration│
│  Consumer  → event handling     │───▶│  Entity   → domain rules │
│  Client    → external APIs      │    │            → validation  │
│  Producer  → event publishing   │◀───│            → transforms  │
│  Repository→ data persistence   │    │                          │
└─────────────────────────────────┘    └──────────────────────────┘
```

**Key Principle:** Business logic lives in the functional core (Service + Entity). IO operations live in the effectful edge. Core defines interfaces; edge implements them (dependency inversion).

## Functional Core

Pure, deterministic components containing all business logic:

### Service Layer
**Responsibility:** Orchestrate business operations, coordinate between entities and repositories

**Characteristics:**
- Pure functions that take data and return results
- No IO operations (database, HTTP, file system)
- Calls repositories through interfaces (dependency injection)
- Composes entity operations into workflows
- Returns success/error results (no exceptions for business logic)

**Example:**
```typescript
class OrderService {
  constructor(
    private orderRepo: OrderRepository,
    private inventoryRepo: InventoryRepository
  ) {}

  async createOrder(request: CreateOrderRequest): Promise<Result<Order>> {
    // 1. Validate with entity
    const order = Order.fromRequest(request);
    const validation = order.validate();
    if (!validation.ok) return validation;

    // 2. Check business rules
    const inventory = await this.inventoryRepo.checkAvailability(order.items);
    if (!inventory.available) return Err('Items not available');

    // 3. Coordinate persistence
    await this.inventoryRepo.reserve(order.items);
    const saved = await this.orderRepo.save(order.toRecord());

    return Ok(Order.fromRecord(saved));
  }
}
```

### Entity Layer
**Responsibility:** Domain models, validation, business rules, data transformations

**Characteristics:**
- Pure data structures with behavior
- All validation logic
- Data transformations (fromRequest, toRecord, toResponse)
- Business rules and invariants
- No IO, no framework dependencies

**Example:**
```typescript
class Order {
  constructor(
    public readonly id: string,
    public readonly customerId: string,
    public readonly items: OrderItem[],
    public readonly status: OrderStatus,
    public readonly total: number
  ) {}

  // Transform from API request
  static fromRequest(req: CreateOrderRequest): Order {
    return new Order(
      generateId(),
      req.customerId,
      req.items.map(i => new OrderItem(i)),
      'pending',
      req.items.reduce((sum, i) => sum + i.price * i.quantity, 0)
    );
  }

  // Transform to database record
  toRecord(): OrderRecord {
    return {
      id: this.id,
      customer_id: this.customerId,
      items: JSON.stringify(this.items),
      status: this.status,
      total: this.total
    };
  }

  // Transform to API response
  toResponse(): OrderResponse {
    return {
      id: this.id,
      customerId: this.customerId,
      items: this.items,
      status: this.status,
      total: this.total
    };
  }

  // Validation with business rules
  validate(): Result<Order> {
    if (this.items.length === 0) {
      return Err('Order must have at least one item');
    }
    if (this.total < 0) {
      return Err('Order total cannot be negative');
    }
    return Ok(this);
  }

  // Business rule: can cancel?
  canCancel(): boolean {
    return ['pending', 'confirmed'].includes(this.status);
  }
}
```

## Effectful Edge

IO-performing components that interact with the outside world:

### Router
**Responsibility:** HTTP request handling, parsing, response formatting

**Characteristics:**
- Parses HTTP requests into domain types
- Calls service layer with parsed data
- Formats service results into HTTP responses
- Handles HTTP-specific concerns (status codes, headers)
- No business logic

**Example:**
```typescript
router.post('/orders', async (req, res) => {
  const result = await orderService.createOrder(req.body);

  if (result.ok) {
    res.status(201).json(result.value.toResponse());
  } else {
    res.status(400).json({ error: result.error });
  }
});
```

### Repository
**Responsibility:** Data persistence and retrieval

**Characteristics:**
- Implements data access interface used by services
- Converts between domain entities and database records
- Handles database queries and transactions
- No business logic or validation

**Example:**
```typescript
class OrderRepository {
  async save(record: OrderRecord): Promise<OrderRecord> {
    return await db.orders.create(record);
  }

  async findById(id: string): Promise<OrderRecord | null> {
    return await db.orders.findOne({ id });
  }
}
```

### Consumer
**Responsibility:** Event consumption and handling

**Characteristics:**
- Listens to event streams
- Parses events into domain types
- Calls service layer to handle events
- No business logic

### Producer
**Responsibility:** Event publishing

**Characteristics:**
- Formats domain events for message bus
- Publishes events to topics/streams
- No business logic

### Client
**Responsibility:** External API interactions

**Characteristics:**
- HTTP clients for third-party APIs
- Converts between external formats and domain types
- Handles retries, timeouts, circuit breaking
- No business logic

## DDD Patterns

### Aggregates
**Definition:** Cluster of domain objects treated as a single unit for data changes

**Rules:**
- One entity is the aggregate root (e.g., Order)
- External references only to the root
- Changes go through the root
- Root enforces invariants

**Example:**
```typescript
class Order {  // Aggregate root
  private items: OrderItem[];  // Part of aggregate

  addItem(item: OrderItem): Result<Order> {
    // Root enforces invariants
    if (this.items.length >= 100) {
      return Err('Order cannot have more than 100 items');
    }
    this.items.push(item);
    return Ok(this);
  }
}
```

### Value Objects
**Definition:** Immutable objects defined by their attributes, not identity

**Characteristics:**
- No identity (equality by value)
- Immutable
- Self-validating

**Example:**
```typescript
class Money {
  constructor(
    public readonly amount: number,
    public readonly currency: string
  ) {
    if (amount < 0) throw new Error('Amount cannot be negative');
  }

  add(other: Money): Money {
    if (this.currency !== other.currency) {
      throw new Error('Cannot add different currencies');
    }
    return new Money(this.amount + other.amount, this.currency);
  }

  equals(other: Money): boolean {
    return this.amount === other.amount && this.currency === other.currency;
  }
}
```

### Domain Events
**Definition:** Something that happened in the domain that domain experts care about

**Example:**
```typescript
class OrderPlaced {
  constructor(
    public readonly orderId: string,
    public readonly customerId: string,
    public readonly occurredAt: Date
  ) {}
}

// Service publishes events
const order = await orderService.createOrder(request);
await eventBus.publish(new OrderPlaced(order.id, order.customerId, new Date()));
```

### Bounded Contexts
**Definition:** Explicit boundaries where a domain model applies

**Guidelines:**
- Each context has its own ubiquitous language
- Contexts communicate via well-defined contracts
- Same concept may have different models in different contexts
- Example: "Customer" in Sales context vs "User" in Auth context

## Putting It Together

How DDD patterns compose with the Functional Core / Effectful Edge architecture:

```
Bounded Context: "Orders"
┌────────────────────────────────────────────────────────────────┐
│                                                                │
│   Effectful Edge                    Functional Core            │
│   ┌──────────────────┐             ┌──────────────────┐       │
│   │ Router           │────────────▶│ Service          │       │
│   │ POST /orders     │             │ OrderService     │       │
│   └──────────────────┘             └────────┬─────────┘       │
│                                              │                 │
│   ┌──────────────────┐             ┌────────▼─────────┐       │
│   │ Repository       │◀────────────│ Aggregate        │       │
│   │ OrderRepository  │             │ Order (root)     │       │
│   └──────────────────┘             │ └─ OrderItem[]   │       │
│                                    │ └─ Money (VO)    │       │
│   ┌──────────────────┐             └──────────────────┘       │
│   │ Producer         │◀── Domain Event: OrderPlaced           │
│   └────────┬─────────┘                                        │
└────────────│───────────────────────────────────────────────────┘
             │
             ▼ Events cross context boundaries
┌────────────────────────────────────────────────────────────────┐
│ Bounded Context: "Inventory"                                   │
│   Consumer ──▶ InventoryService ──▶ StockLevel (Aggregate)     │
└────────────────────────────────────────────────────────────────┘
```

**Composition Rules:**

| DDD Pattern | Maps To | Location |
|-------------|---------|----------|
| Bounded Context | Module/Package boundary | Contains all layers |
| Aggregate | Entity cluster | Functional Core |
| Aggregate Root | Primary Entity | Entity layer |
| Value Object | Immutable type | Entity layer |
| Domain Event | Event class | Published from Service, consumed by edge |
| Repository | Data access | Effectful Edge (interface in Core) |

**Key Interactions:**
1. **Router → Service**: HTTP request parsed, passed to service
2. **Service → Aggregate**: Service orchestrates aggregate operations
3. **Aggregate → Repository**: Service uses repository to persist aggregate
4. **Service → Producer**: Service publishes domain events after state change
5. **Consumer → Service**: Events from other contexts trigger service operations

## Data Modeling

### Entity Design
1. Start with domain concepts from product requirements
2. Identify aggregates and their boundaries
3. Define attributes and relationships
4. Add validation rules and invariants
5. Include transformation methods

### Schema Patterns

**Single Table Design (DynamoDB):**
```typescript
{
  PK: 'ORDER#123',
  SK: 'ORDER#123',
  type: 'Order',
  customerId: 'CUST#456',
  // ... other attributes
}

// Access patterns drive key design
// PK = partition key, SK = sort key
```

**Relational Design:**
```sql
CREATE TABLE orders (
  id UUID PRIMARY KEY,
  customer_id UUID REFERENCES customers(id),
  status VARCHAR(20),
  total DECIMAL(10,2),
  created_at TIMESTAMP
);

CREATE INDEX idx_orders_customer ON orders(customer_id);
```

### Access Patterns
List all data access patterns before designing schema:
- Find order by ID
- List orders by customer
- List pending orders
- Count orders by status

Design indexes and keys to support these patterns efficiently.

## API Design

### REST Conventions

**Resource-Oriented:**
```
GET    /orders          # List orders
POST   /orders          # Create order
GET    /orders/:id      # Get order
PATCH  /orders/:id      # Update order
DELETE /orders/:id      # Delete order
```

**Actions (when CRUD doesn't fit):**
```
POST /orders/:id/cancel   # Cancel order
POST /orders/:id/ship     # Ship order
```

### Request/Response Contracts

**Request:**
```typescript
interface CreateOrderRequest {
  customerId: string;
  items: Array<{
    productId: string;
    quantity: number;
  }>;
}
```

**Response:**
```typescript
interface OrderResponse {
  id: string;
  customerId: string;
  items: OrderItem[];
  status: string;
  total: number;
  createdAt: string;
}
```

**Error Response:**
```typescript
interface ErrorResponse {
  error: string;
  code?: string;
  details?: Record<string, unknown>;
}
```

## Task Breakdown

### Bottom-Up Dependency Ordering

Implementation order follows dependency chain:

```
1. Entity   → Domain models, validation, transforms
2. Repository → Data access interfaces and implementations
3. Service  → Business logic orchestration
4. Router   → HTTP endpoints
```

**Rationale:** Each layer depends on layers below. Can't implement service without entity, can't implement router without service.

### Task Granularity

**One task per layer:**
- Implement Order entity with validation
- Implement OrderRepository with data access
- Implement OrderService with business logic
- Implement order API endpoints

**For complex features, break down further:**
- Entity: Order, OrderItem, OrderStatus
- Repository: OrderRepository, InventoryRepository
- Service: OrderService, PaymentService
- Router: Order routes, Payment routes

### Cross-Cutting Concerns

**After core layers:**
- Add error handling middleware
- Add authentication/authorization
- Add logging and monitoring
- Add API documentation

## Component Matrix

Quick reference for where things belong:

| Concern | Component | Layer | Testability |
|---------|-----------|-------|-------------|
| Domain model | Entity | Core | Unit test (pure) |
| Validation | Entity | Core | Unit test (pure) |
| Business rules | Entity | Core | Unit test (pure) |
| Orchestration | Service | Core | Unit test (stub repos) |
| Data transforms | Entity | Core | Unit test (pure) |
| HTTP parsing | Router | Edge | Integration test |
| Data access | Repository | Edge | Integration test |
| External APIs | Client | Edge | Integration test |
| Event handling | Consumer | Edge | Integration test |
| Event publishing | Producer | Edge | Integration test |

## Architect → Testing Flow

Architectural decisions inform testing strategy:

```
Architect Outputs           →    Testing Inputs
────────────────────────────────────────────────
Component responsibilities  →    What to test
Layer boundaries           →    Where to test
Pure vs effectful          →    Unit vs integration
Entity transformations     →    Property-based tests
Service orchestration      →    Stub-driven tests
```

The testing skill uses architectural structure to determine:
- What gets unit tested (core) vs integration tested (edge)
- Where to place test boundaries
- What to stub and what to test for real
- What test cases validate business rules
