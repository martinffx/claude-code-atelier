# Layer Boundary Testing

Test at component boundaries, not internals.

## What to Test at Each Layer

### Entity Layer
- ✅ Domain logic
- ✅ Validation
- ✅ Transformations (from_request, to_response)
- ❌ Simple getters/setters

### Repository Layer
- ✅ CRUD operations
- ✅ Query logic
- ✅ Entity ↔ Record transformations
- ❌ ORM framework code

### Service Layer
- ✅ Business workflows
- ✅ Orchestration logic
- ✅ Error handling
- ❌ Trivial delegation

### Router Layer
- ✅ Request validation
- ✅ Response serialization
- ✅ Status codes
- ❌ Framework routing

## Examples

```python
# Entity: Test transformations
def test_user_from_request():
    request = CreateUserRequest(email="test@example.com", age=25)
    user = User.from_request(request)
    assert user.email == "test@example.com"

# Service: Stub repository
def test_create_user_service():
    mock_repo = Mock()
    service = UserService(mock_repo)
    service.create(user_data)
    mock_repo.save.assert_called_once()

# Repository: Use test database
def test_repository_save(test_db):
    repo = UserRepository(test_db)
    user = User(...)
    saved = repo.save(user)
    assert test_db.query(UserRecord).count() == 1

# Router: Use TestClient
def test_create_user_endpoint():
    client = TestClient(app)
    response = client.post("/users", json={...})
    assert response.status_code == 201
```
