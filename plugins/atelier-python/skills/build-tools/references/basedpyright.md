# basedpyright - Strict Type Checking

basedpyright is a fork of pyright with additional features and stricter defaults. Fast, strict Python type checker.

## Configuration

### Basic Setup

```toml
[tool.basedpyright]
# Type checking mode
typeCheckingMode = "strict"  # off, basic, standard, strict

# Python version
pythonVersion = "3.12"

# Include/exclude
include = ["src"]
exclude = [
    "**/__pycache__",
    ".venv",
    "build",
    "dist",
]

# Specific file to exclude
ignore = ["src/legacy"]
```

### Type Checking Modes

**off**: No type checking

**basic**: Minimal type checking
- Undefined variables
- Unknown member access
- Missing imports

**standard**: Moderate type checking
- All basic checks
- Type inconsistencies
- Some missing type annotations

**strict**: Maximum type checking (recommended)
- All standard checks
- All functions must have type hints
- All class variables must be annotated
- Strict optional (None handling)

## Report Diagnostics

Fine-tune specific diagnostic categories:

```toml
[tool.basedpyright]
typeCheckingMode = "strict"

# Errors (will fail type check)
reportMissingImports = "error"
reportUndefinedVariable = "error"

# Warnings (won't fail, but shown)
reportUnusedVariable = "warning"
reportUnusedImport = "warning"

# Information (low priority)
reportUnusedClass = "information"
reportUnusedFunction = "information"

# Disabled (don't report)
reportMissingTypeStubs = "none"
reportPrivateUsage = "none"
```

### Common Diagnostics

```toml
[tool.basedpyright]
typeCheckingMode = "strict"

# Missing annotations
reportMissingTypeArgument = "error"
reportUnknownParameterType = "error"
reportUnknownVariableType = "error"

# Type inconsistencies
reportGeneralTypeIssues = "error"
reportIncompatibleMethodOverride = "error"

# Optional/None handling
reportOptionalMemberAccess = "error"
reportOptionalCall = "error"
reportOptionalIterable = "error"

# Import issues
reportMissingImports = "error"
reportMissingModuleSource = "error"

# Unused code
reportUnusedVariable = "warning"
reportUnusedImport = "warning"
reportUnusedClass = "information"
reportUnusedFunction = "information"

# Less strict
reportPrivateUsage = "none"
reportMissingTypeStubs = "none"
```

## Type Hints

### Function Annotations

```python
# ✅ Proper type hints
def calculate_discount(price: Decimal, rate: Decimal) -> Decimal:
    return price * rate

# ❌ Missing return type
def calculate_discount(price: Decimal, rate: Decimal):
    return price * rate

# ❌ Missing parameter types
def calculate_discount(price, rate) -> Decimal:
    return price * rate
```

### Variable Annotations

```python
# ✅ Annotated variables
name: str = "Alice"
age: int = 30
prices: list[Decimal] = []

# ❌ Unannotated variables (in strict mode)
name = "Alice"
age = 30
prices = []
```

### Class Attributes

```python
from dataclasses import dataclass
from decimal import Decimal

# ✅ Annotated class attributes
@dataclass
class Product:
    name: str
    price: Decimal
    in_stock: bool = True

# ❌ Unannotated attributes
class Product:
    def __init__(self, name, price):
        self.name = name
        self.price = price
```

## Generic Types

### Built-in Generics (Python 3.9+)

```python
# ✅ Modern generic syntax
items: list[str] = ["a", "b"]
mapping: dict[str, int] = {"a": 1}
result: tuple[str, int] = ("a", 1)
optional: str | None = None

# ❌ Old typing module (Python 3.8)
from typing import List, Dict, Tuple, Optional
items: List[str] = ["a", "b"]
mapping: Dict[str, int] = {"a": 1}
result: Tuple[str, int] = ("a", 1)
optional: Optional[str] = None
```

### Custom Generics

```python
from typing import TypeVar, Generic

T = TypeVar("T")

class Repository(Generic[T]):
    """Generic repository"""

    def get(self, id: int) -> T | None:
        ...

    def save(self, entity: T) -> None:
        ...

# Usage
class User:
    id: int
    name: str

user_repo = Repository[User]()
user = user_repo.get(1)  # Type is User | None
```

### Protocol (Structural Typing)

```python
from typing import Protocol

class Comparable(Protocol):
    """Structural type - any class with __lt__"""

    def __lt__(self, other: object) -> bool:
        ...

def find_min(items: list[Comparable]) -> Comparable:
    """Works with any comparable type"""
    return min(items)

# Works without inheritance
@dataclass
class Product:
    price: Decimal

    def __lt__(self, other: object) -> bool:
        if not isinstance(other, Product):
            return NotImplemented
        return self.price < other.price

products: list[Product] = [...]
cheapest = find_min(products)  # Type checks!
```

## Optional/None Handling

### Strict Optional

With strict mode, basedpyright enforces None checking:

```python
def get_user(user_id: int) -> User | None:
    """May return None"""
    ...

# ❌ Error: Object may be None
user = get_user(1)
print(user.name)  # Type error!

# ✅ Check for None
user = get_user(1)
if user is not None:
    print(user.name)  # OK

# ✅ Or use assertion
user = get_user(1)
assert user is not None
print(user.name)  # OK
```

### Optional Chaining

```python
from typing import Optional

# ❌ Multiple None checks
def get_email(user: User | None) -> str | None:
    if user is None:
        return None
    if user.profile is None:
        return None
    return user.profile.email

# ✅ Use early return
def get_email(user: User | None) -> str | None:
    if user is None or user.profile is None:
        return None
    return user.profile.email
```

## Type Narrowing

basedpyright understands type narrowing:

```python
from typing import Union

def process(value: int | str) -> str:
    if isinstance(value, int):
        # basedpyright knows value is int here
        return str(value * 2)
    else:
        # basedpyright knows value is str here
        return value.upper()

# With None
def process(value: User | None) -> str:
    if value is None:
        return "Unknown"
    # basedpyright knows value is User here
    return value.name
```

## Union Types

### Modern Syntax (Python 3.10+)

```python
# ✅ Union with |
def process(value: int | str) -> bool:
    ...

# ❌ Old Union syntax
from typing import Union
def process(value: Union[int, str]) -> bool:
    ...
```

### Handling Unions

```python
from typing import assert_never

def process(value: int | str | bool) -> str:
    if isinstance(value, int):
        return str(value)
    elif isinstance(value, str):
        return value
    elif isinstance(value, bool):
        return "yes" if value else "no"
    else:
        # Exhaustiveness check - catches missing cases
        assert_never(value)
```

## TypedDict

For typed dictionaries:

```python
from typing import TypedDict

class UserDict(TypedDict):
    id: int
    name: str
    email: str

def create_user(data: UserDict) -> User:
    """Type-safe dict handling"""
    return User(
        id=data["id"],
        name=data["name"],
        email=data["email"],
    )

# ✅ Type checks
user_data: UserDict = {"id": 1, "name": "Alice", "email": "alice@example.com"}
create_user(user_data)

# ❌ Type error: missing required key
invalid_data: UserDict = {"id": 1, "name": "Alice"}
```

### NotRequired (Python 3.11+)

```python
from typing import TypedDict, NotRequired

class UserDict(TypedDict):
    id: int
    name: str
    email: NotRequired[str]  # Optional field

data: UserDict = {"id": 1, "name": "Alice"}  # OK, email not required
```

## Type Guards

Custom type narrowing:

```python
from typing import TypeGuard

def is_user(obj: object) -> TypeGuard[User]:
    """Type guard - narrows type"""
    return isinstance(obj, User) and hasattr(obj, "name")

def process(obj: object) -> str:
    if is_user(obj):
        # basedpyright knows obj is User here
        return obj.name
    return "Unknown"
```

## Overload

Multiple signatures for single function:

```python
from typing import overload

@overload
def process(value: int) -> str: ...

@overload
def process(value: str) -> int: ...

def process(value: int | str) -> int | str:
    """Implementation must accept all overloads"""
    if isinstance(value, int):
        return str(value)
    return len(value)

# Type checks correctly
result1: str = process(42)  # OK
result2: int = process("hello")  # OK
```

## ParamSpec and Concatenate

For preserving function signatures:

```python
from typing import Callable, ParamSpec, TypeVar, Concatenate

P = ParamSpec("P")
R = TypeVar("R")

def with_logging(func: Callable[P, R]) -> Callable[P, R]:
    """Decorator that preserves signature"""
    def wrapper(*args: P.args, **kwargs: P.kwargs) -> R:
        print(f"Calling {func.__name__}")
        return func(*args, **kwargs)
    return wrapper

@with_logging
def add(a: int, b: int) -> int:
    return a + b

# Type checks correctly
result: int = add(1, 2)  # OK
# add("1", "2")  # Type error!
```

## Literal Types

For specific values:

```python
from typing import Literal

Status = Literal["pending", "active", "inactive"]

def set_status(status: Status) -> None:
    ...

set_status("pending")  # OK
set_status("invalid")  # Type error!
```

## Usage

### Command Line

```bash
# Type check project
basedpyright

# Type check specific file
basedpyright src/main.py

# Watch mode
basedpyright --watch

# Verbose output
basedpyright --verbose

# Create config
basedpyright --createdefaultconfig
```

### VS Code Integration

Install Pylance extension (basedpyright-compatible):

**.vscode/settings.json:**

```json
{
  "python.analysis.typeCheckingMode": "strict",
  "python.analysis.diagnosticMode": "workspace",
  "python.analysis.autoImportCompletions": true,
  "python.analysis.inlayHints.functionReturnTypes": true,
  "python.analysis.inlayHints.variableTypes": true
}
```

### CI Integration

```yaml
- name: Type check
  run: uv run basedpyright
```

## Ignoring Errors

### Inline Ignore

```python
# Type: ignore for single line
result = unsafe_operation()  # type: ignore

# Type: ignore with code
result = unsafe_operation()  # type: ignore[reportGeneralTypeIssues]
```

### File-level Ignore

```python
# basedpyright: ignore

# Entire file ignored
```

### Per-file Configuration

```toml
[tool.basedpyright]
include = ["src"]

[[tool.basedpyright.executionEnvironments]]
root = "tests"
reportUnusedImport = "none"
```

## Best Practices

1. **Use strict mode**: Catch bugs early
   ```toml
   typeCheckingMode = "strict"
   ```

2. **Annotate all functions**: Make intent clear
   ```python
   def process(data: dict[str, int]) -> list[str]:
       ...
   ```

3. **Handle None explicitly**: Don't ignore optionals
   ```python
   if value is not None:
       use(value)
   ```

4. **Use modern syntax**: Python 3.10+ union types
   ```python
   str | None  # not Optional[str]
   list[int]   # not List[int]
   ```

5. **Protocol over inheritance**: Structural typing
   ```python
   class Readable(Protocol):
       def read(self) -> str: ...
   ```

## Common Patterns

### Repository with Generic

```python
from typing import TypeVar, Generic, Protocol

class Entity(Protocol):
    id: int

T = TypeVar("T", bound=Entity)

class Repository(Generic[T]):
    def get(self, id: int) -> T | None:
        ...

    def save(self, entity: T) -> T:
        ...
```

### Builder Pattern

```python
from typing import Self  # Python 3.11+

class UserBuilder:
    def __init__(self) -> None:
        self._name: str | None = None
        self._email: str | None = None

    def with_name(self, name: str) -> Self:
        self._name = name
        return self

    def with_email(self, email: str) -> Self:
        self._email = email
        return self

    def build(self) -> User:
        assert self._name is not None
        assert self._email is not None
        return User(name=self._name, email=self._email)
```

## Summary

**Configuration:**
- Use strict mode for maximum safety
- Configure diagnostics per project needs
- Exclude generated/legacy code

**Type hints:**
- Annotate all functions and variables
- Use modern syntax (Python 3.10+ unions)
- Handle None explicitly

**Advanced types:**
- Generic for reusable types
- Protocol for structural typing
- TypedDict for typed dictionaries
- Literal for specific values

**Workflow:**
1. Enable strict mode
2. Add type hints
3. Run `basedpyright`
4. Fix type errors
5. Commit type-safe code
