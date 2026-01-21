---
name: python:testing
description: Stub-Driven TDD and layer boundary testing with pytest. Use when writing tests, deciding what to test, testing at component boundaries, or implementing test-driven development.
user-invocable: false
---

# Testing with pytest

Stub-Driven TDD and layer boundary testing patterns for Python applications.

## Core Principle: Test at Boundaries

Test component boundaries, not internal implementation:

```
Router → Service → Repository → Entity → Database
   ↓        ↓           ↓          ↓
 Test    Test        Test       Test
```

**Test at each boundary:**
- Router: HTTP request → Service call
- Service: Business logic orchestration
- Repository: Data access abstraction
- Entity: Domain logic and transformations

**Don't test:**
- Framework code (FastAPI, SQLAlchemy)
- Trivial getters/setters
- Internal implementation details

## Stub-Driven TDD Workflow

1. **Stub**: Create function signature with `pass`
2. **Test**: Write test for expected behavior
3. **Implement**: Make test pass
4. **Refactor**: Clean up code

```python
# 1. Stub
def calculate_discount(total: Decimal) -> Decimal:
    pass

# 2. Test
def test_discount_for_large_order():
    result = calculate_discount(Decimal("150"))
    assert result == Decimal("15")  # 10% discount

# 3. Implement
def calculate_discount(total: Decimal) -> Decimal:
    if total > 100:
        return total * Decimal("0.1")
    return Decimal("0")

# 4. Refactor (if needed)
```

## pytest Basics

### Test Structure

```python
import pytest
from decimal import Decimal

def test_calculate_discount():
    """Test discount calculation"""
    # Arrange
    total = Decimal("150")

    # Act
    result = calculate_discount(total)

    # Assert
    assert result == Decimal("15")
```

### Running Tests

```bash
# Run all tests
pytest

# Run specific file
pytest tests/test_entities.py

# Run specific test
pytest tests/test_entities.py::test_calculate_discount

# Verbose output
pytest -v

# Stop on first failure
pytest -x

# Show print statements
pytest -s

# Run with coverage
pytest --cov=src --cov-report=html
```

## Entity Testing

Test domain logic and transformations:

```python
from entities import Product
from decimal import Decimal

def test_product_creation():
    """Test entity creation"""
    product = Product(
        id=uuid4(),
        name="Widget",
        price=Decimal("9.99"),
    )
    assert product.name == "Widget"
    assert product.price == Decimal("9.99")

def test_product_from_request():
    """Test transformation from request"""
    request = CreateProductRequest(
        name="Widget",
        price=Decimal("9.99"),
    )

    product = Product.from_request(request)

    assert product.name == "Widget"
    assert product.price == Decimal("9.99")
    assert isinstance(product.id, UUID)

def test_product_to_response():
    """Test transformation to response"""
    product = Product(
        id=uuid4(),
        name="Widget",
        price=Decimal("9.99"),
    )

    response = product.to_response()

    assert response.id == product.id
    assert response.name == "Widget"
```

## Service Testing

Test business logic with stubbed dependencies:

```python
from unittest.mock import Mock
import pytest

def test_create_product():
    """Test product creation service"""
    # Stub repository
    mock_repo = Mock()
    mock_repo.save.return_value = Product(
        id=uuid4(),
        name="Widget",
        price=Decimal("9.99"),
    )

    # Test service
    service = ProductService(repo=mock_repo)
    request = CreateProductRequest(name="Widget", price=Decimal("9.99"))

    result = service.create(request)

    # Verify service called repository
    assert mock_repo.save.called
    assert result.name == "Widget"
```

## Repository Testing

Test data access with test database:

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

@pytest.fixture
def test_db():
    """Test database session"""
    engine = create_engine("sqlite:///:memory:")
    Base.metadata.create_all(engine)
    SessionLocal = sessionmaker(bind=engine)
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

def test_repository_save(test_db):
    """Test repository save"""
    repo = ProductRepository(test_db)

    product = Product(
        id=uuid4(),
        name="Widget",
        price=Decimal("9.99"),
    )

    saved = repo.save(product)

    assert saved.id == product.id
    assert test_db.query(ProductRecord).count() == 1

def test_repository_find_by_id(test_db):
    """Test repository find"""
    repo = ProductRepository(test_db)

    product = Product(id=uuid4(), name="Widget", price=Decimal("9.99"))
    repo.save(product)

    found = repo.find_by_id(product.id)

    assert found is not None
    assert found.id == product.id
```

## Router Testing

Test HTTP layer with TestClient:

```python
from fastapi.testclient import TestClient

def test_create_product_endpoint():
    """Test product creation endpoint"""
    client = TestClient(app)

    response = client.post(
        "/products",
        json={"name": "Widget", "price": 9.99},
    )

    assert response.status_code == 201
    data = response.json()
    assert data["name"] == "Widget"
    assert data["price"] == 9.99

def test_get_product_endpoint():
    """Test get product endpoint"""
    client = TestClient(app)

    # Create product
    create_response = client.post(
        "/products",
        json={"name": "Widget", "price": 9.99},
    )
    product_id = create_response.json()["id"]

    # Get product
    response = client.get(f"/products/{product_id}")

    assert response.status_code == 200
    assert response.json()["id"] == product_id
```

## Fixtures

Reusable test setup with pytest fixtures:

```python
import pytest

@pytest.fixture
def sample_product():
    """Sample product for testing"""
    return Product(
        id=uuid4(),
        name="Widget",
        price=Decimal("9.99"),
    )

@pytest.fixture
def mock_repository():
    """Mock repository"""
    return Mock(spec=ProductRepository)

def test_with_fixtures(sample_product, mock_repository):
    """Test using fixtures"""
    service = ProductService(mock_repository)
    # Use sample_product and mock_repository
```

## Parametrized Tests

Test multiple inputs:

```python
@pytest.mark.parametrize("total,expected", [
    (Decimal("50"), Decimal("0")),    # No discount
    (Decimal("100"), Decimal("0")),   # No discount
    (Decimal("150"), Decimal("15")),  # 10% discount
    (Decimal("200"), Decimal("20")),  # 10% discount
])
def test_calculate_discount(total, expected):
    result = calculate_discount(total)
    assert result == expected
```

## Mocking

Stub external dependencies:

```python
from unittest.mock import Mock, patch, MagicMock

def test_with_mock():
    """Test with mocked dependency"""
    mock_db = Mock()
    mock_db.query.return_value.first.return_value = UserRecord(id=1, name="Alice")

    service = UserService(mock_db)
    user = service.get_user(1)

    assert user.name == "Alice"
    mock_db.query.assert_called_once()

@patch("my_module.external_api_call")
def test_with_patch(mock_api):
    """Test with patched function"""
    mock_api.return_value = {"status": "success"}

    result = process_data()

    assert result["status"] == "success"
    mock_api.assert_called_once()
```

See references/ for pytest configuration, boundary testing patterns, and mocking strategies.
