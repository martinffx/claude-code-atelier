# Mocking and Stubbing Strategies

## unittest.mock

### Mock Objects

```python
from unittest.mock import Mock

# Create mock
mock_repo = Mock()

# Configure return value
mock_repo.get.return_value = User(id=1, name="Alice")

# Call mock
user = mock_repo.get(1)

# Verify call
mock_repo.get.assert_called_once_with(1)
```

### MagicMock

```python
from unittest.mock import MagicMock

# Supports magic methods
mock_db = MagicMock()
mock_db.query.return_value.filter.return_value.first.return_value = UserRecord()

# Chain calls
user = mock_db.query(User).filter(User.id == 1).first()
```

### patch Decorator

```python
from unittest.mock import patch

@patch("my_module.DatabaseSession")
def test_with_patched_db(mock_db_class):
    mock_db_class.return_value = Mock()

    service = UserService()
    # Uses patched DatabaseSession
```

### spec Parameter

```python
# Mock with spec (only allows real attributes)
mock_repo = Mock(spec=UserRepository)

# This works (real method)
mock_repo.save()

# This fails (AttributeError)
mock_repo.invalid_method()
```

## pytest-mock

```python
def test_with_mocker(mocker):
    """pytest-mock fixture"""
    mock_db = mocker.patch("my_module.get_db")
    mock_db.return_value = Mock()

    # Test code
```

## Verification

```python
# Called once
mock.method.assert_called_once()

# Called with specific args
mock.method.assert_called_with(arg1, arg2)

# Called any args
mock.method.assert_called()

# Never called
mock.method.assert_not_called()

# Call count
assert mock.method.call_count == 2
```
