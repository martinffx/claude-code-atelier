---
name: python:architecture
description: Python application architecture with functional core, effectful shell, DDD, and data modeling. Use when designing application layers, separating pure business logic from IO, defining domain models, implementing validation, or structuring bounded contexts.
user-invocable: false
---

# Python Application Architecture

Modern Python application architecture following functional core / imperative shell pattern, Domain-Driven Design, and type-safe data modeling.

## Core Principle: Functional Core / Imperative Shell

Separate pure business logic from side effects.

**Functional Core:**
- Pure functions with no side effects
- Business logic and domain rules
- Deterministic and easily testable
- No IO operations (no database, HTTP, files, time)

**Imperative Shell:**
- Handles all side effects
- Coordinates external dependencies
- Thin layer around functional core
- Orchestrates IO operations

```python
# ❌ Bad: Mixed concerns
def process_order(order_id: str) -> None:
    order = db.get_order(order_id)  # IO
    if order.total > 100:  # Business logic
        order.discount = 0.1
    db.save_order(order)  # IO

# ✅ Good: Separated concerns
def calculate_discount(total: Decimal) -> Decimal:
    """Pure function - business logic only"""
    if total > 100:
        return Decimal("0.1")
    return Decimal("0")

def process_order(order_id: str, repo: OrderRepository) -> None:
    """Imperative shell - orchestrates IO"""
    order = repo.get(order_id)  # IO
    discount = calculate_discount(order.total)  # Pure
    order.apply_discount(discount)  # Pure
    repo.save(order)  # IO
```

## Layered Architecture

Follow bottom-up dependency flow:

```
Router/Handler → Service → Repository → Entity → Database
```

Each layer depends only on layers below.

**Entity Layer:**
- Domain models and value objects
- Business rules and validation
- Data transformations (fromRequest, toRecord, toResponse)
- No dependencies on external frameworks

**Repository Layer:**
- Abstract storage interface
- Hides database implementation details
- Returns domain entities, not raw records

**Service Layer:**
- Business workflows and use cases
- Orchestrates entities and repositories
- Transaction boundaries

**Router/Handler Layer:**
- HTTP request/response handling
- Input validation and serialization
- Delegates to services

## Domain-Driven Design Patterns

### Entities and Value Objects

**Entity:**
- Has identity (ID field)
- Mutable state
- Lifecycle management

```python
from dataclasses import dataclass
from uuid import UUID
from decimal import Decimal

@dataclass
class Order:
    """Entity - has identity"""
    id: UUID
    customer_id: UUID
    total: Decimal
    status: str

    def apply_discount(self, rate: Decimal) -> None:
        """Business rule - encapsulated in entity"""
        if self.status == "pending":
            self.total = self.total * (1 - rate)
```

**Value Object:**
- No identity
- Immutable
- Defined by attributes

```python
from dataclasses import dataclass

@dataclass(frozen=True)
class Money:
    """Value object - immutable, no identity"""
    amount: Decimal
    currency: str

    def add(self, other: "Money") -> "Money":
        if self.currency != other.currency:
            raise ValueError("Cannot add different currencies")
        return Money(self.amount + other.amount, self.currency)
```

### Aggregates and Aggregate Roots

**Aggregate:**
- Cluster of entities and value objects
- Transaction boundary
- Consistency rules

**Aggregate Root:**
- Entry point to aggregate
- Controls access to internal entities
- Enforces invariants

```python
@dataclass
class OrderLine:
    product_id: UUID
    quantity: int
    price: Decimal

@dataclass
class Order:
    """Aggregate root"""
    id: UUID
    customer_id: UUID
    lines: list[OrderLine]

    def add_line(self, product_id: UUID, quantity: int, price: Decimal) -> None:
        """Control access through aggregate root"""
        if quantity <= 0:
            raise ValueError("Quantity must be positive")
        self.lines.append(OrderLine(product_id, quantity, price))

    def total(self) -> Decimal:
        """Calculated property"""
        return sum(line.quantity * line.price for line in self.lines)
```

### Repository Pattern

Abstract storage behind interface:

```python
from abc import ABC, abstractmethod
from typing import Optional

class OrderRepository(ABC):
    """Abstract repository - no implementation details"""

    @abstractmethod
    def get(self, order_id: UUID) -> Optional[Order]:
        pass

    @abstractmethod
    def save(self, order: Order) -> None:
        pass

    @abstractmethod
    def find_by_customer(self, customer_id: UUID) -> list[Order]:
        pass

class PostgresOrderRepository(OrderRepository):
    """Concrete implementation - hidden behind interface"""

    def __init__(self, session: Session):
        self.session = session

    def get(self, order_id: UUID) -> Optional[Order]:
        record = self.session.get(OrderRecord, order_id)
        return Order.from_record(record) if record else None

    def save(self, order: Order) -> None:
        record = order.to_record()
        self.session.merge(record)
        self.session.commit()
```

### Application Services vs Domain Services

**Domain Service:**
- Business logic that doesn't fit in entity
- Operates on multiple entities
- Part of functional core

```python
def transfer_funds(from_account: Account, to_account: Account, amount: Decimal) -> None:
    """Domain service - pure business logic"""
    if from_account.balance < amount:
        raise InsufficientFundsError()
    from_account.debit(amount)
    to_account.credit(amount)
```

**Application Service:**
- Use case orchestration
- Coordinates repositories and domain services
- Part of imperative shell

```python
class TransferService:
    """Application service - orchestrates IO"""

    def __init__(self, account_repo: AccountRepository):
        self.account_repo = account_repo

    def transfer(self, from_id: UUID, to_id: UUID, amount: Decimal) -> None:
        from_account = self.account_repo.get(from_id)
        to_account = self.account_repo.get(to_id)

        transfer_funds(from_account, to_account, amount)  # Domain service

        self.account_repo.save(from_account)
        self.account_repo.save(to_account)
```

### Bounded Contexts

Separate models for different subdomains:

```python
# Ordering context
@dataclass
class Product:
    id: UUID
    name: str
    price: Decimal

# Shipping context
@dataclass
class Product:
    id: UUID
    weight: Decimal
    dimensions: tuple[int, int, int]
```

Use context mapping to integrate:
- Shared Kernel: Shared types across contexts
- Customer/Supplier: Downstream depends on upstream
- Anti-Corruption Layer: Translate between contexts

## Data Modeling with dataclasses and Pydantic

### dataclasses for Domain Models

Use for internal domain logic:

```python
from dataclasses import dataclass, field
from datetime import datetime
from uuid import UUID, uuid4

@dataclass
class User:
    id: UUID = field(default_factory=uuid4)
    email: str
    created_at: datetime = field(default_factory=datetime.now)

    def __post_init__(self):
        """Validation in __post_init__"""
        if "@" not in self.email:
            raise ValueError("Invalid email")
```

### Pydantic for API Boundaries

Use for validation at system edges:

```python
from pydantic import BaseModel, EmailStr, Field, field_validator
from decimal import Decimal

class CreateOrderRequest(BaseModel):
    """Request validation at API boundary"""
    customer_email: EmailStr
    items: list[OrderItemRequest]

    @field_validator("items")
    @classmethod
    def validate_items(cls, v):
        if not v:
            raise ValueError("Order must have at least one item")
        return v

class OrderItemRequest(BaseModel):
    product_id: UUID
    quantity: int = Field(gt=0)

class OrderResponse(BaseModel):
    """Response serialization"""
    id: UUID
    total: Decimal
    status: str

    model_config = {"from_attributes": True}
```

### Entity Data Transformations

Entities handle all transformations:

```python
@dataclass
class Order:
    id: UUID
    customer_id: UUID
    total: Decimal
    status: str

    @classmethod
    def from_request(cls, req: CreateOrderRequest, customer_id: UUID) -> "Order":
        """Transform request → entity"""
        total = sum(item.quantity * item.price for item in req.items)
        return cls(
            id=uuid4(),
            customer_id=customer_id,
            total=total,
            status="pending"
        )

    def to_record(self) -> OrderRecord:
        """Transform entity → database record"""
        return OrderRecord(
            id=self.id,
            customer_id=self.customer_id,
            total=self.total,
            status=self.status
        )

    @classmethod
    def from_record(cls, record: OrderRecord) -> "Order":
        """Transform database record → entity"""
        return cls(
            id=record.id,
            customer_id=record.customer_id,
            total=record.total,
            status=record.status
        )

    def to_response(self) -> OrderResponse:
        """Transform entity → response"""
        return OrderResponse(
            id=self.id,
            total=self.total,
            status=self.status
        )
```

## Immutability Patterns

Use frozen dataclasses for immutability:

```python
from dataclasses import dataclass, replace

@dataclass(frozen=True)
class Money:
    amount: Decimal
    currency: str

    def add(self, other: "Money") -> "Money":
        """Return new instance instead of mutating"""
        return replace(self, amount=self.amount + other.amount)
```

Or use Pydantic's frozen config:

```python
from pydantic import BaseModel

class Money(BaseModel):
    amount: Decimal
    currency: str

    model_config = {"frozen": True}
```

## Best Practices

1. **Pure functions first**: Write business logic as pure functions
2. **Entity encapsulation**: Keep business rules inside entities
3. **Repository abstraction**: Hide storage details behind interface
4. **Validate at boundaries**: Use Pydantic at API edges, simple validation internally
5. **Immutability**: Prefer immutable value objects
6. **Single Responsibility**: Each layer has one reason to change
7. **Dependency direction**: Always depend on abstractions, not implementations

## Anti-Patterns

❌ **Anemic Domain Model**: Entities with no behavior, only getters/setters
❌ **Transaction Script**: All logic in service layer, entities are just data
❌ **Leaky Abstraction**: Repository exposing database details
❌ **God Object**: Entity with too many responsibilities
❌ **Mixed Concerns**: Business logic calling IO directly

See references/ for detailed examples of functional core patterns, Domain-Driven Design, and data modeling strategies.
