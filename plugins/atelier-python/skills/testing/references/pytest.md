# pytest Configuration and Patterns

## Configuration (pyproject.toml)

```toml
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py", "*_test.py"]
python_classes = ["Test*"]
python_functions = ["test_*"]
addopts = [
    "-ra",              # Show summary
    "--strict-markers",
    "--strict-config",
    "-v",
]
markers = [
    "slow: slow tests",
    "integration: integration tests",
    "unit: unit tests",
]
```

## Fixtures

```python
@pytest.fixture
def db_session():
    """Database session fixture"""
    session = SessionLocal()
    try:
        yield session
    finally:
        session.close()

@pytest.fixture(scope="module")
def test_client():
    """FastAPI test client"""
    return TestClient(app)

@pytest.fixture(autouse=True)
def reset_db(db_session):
    """Reset database before each test"""
    Base.metadata.drop_all(bind=engine)
    Base.metadata.create_all(bind=engine)
```

## Markers

```python
@pytest.mark.slow
def test_expensive_operation():
    ...

@pytest.mark.integration
def test_database_integration():
    ...

# Run only unit tests
# pytest -m unit

# Skip slow tests
# pytest -m "not slow"
```

## Parametrize

```python
@pytest.mark.parametrize("input,expected", [
    (1, 2),
    (2, 4),
    (3, 6),
])
def test_double(input, expected):
    assert double(input) == expected
```

## Coverage

```bash
# Run with coverage
pytest --cov=src

# HTML report
pytest --cov=src --cov-report=html

# Fail if coverage below threshold
pytest --cov=src --cov-fail-under=80
```
