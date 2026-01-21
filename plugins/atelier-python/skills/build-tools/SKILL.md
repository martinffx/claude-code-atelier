---
name: python:build-tools
description: Python project tooling with uv, mise, ruff, basedpyright, and pytest. Use when setting up pyproject.toml, running builds, typechecking, configuring tests, linting, formatting, or managing Python environments.
user-invocable: false
---

# Python Build Tools

Modern Python development tooling using uv, mise, ruff, basedpyright, and pytest.

## Core Tools

**uv** - Fast Python package manager (Rust-based)
- Replaces pip, pip-tools, virtualenv
- 10-100x faster than pip
- Compatible with pip/setuptools
- Workspace support

**mise** - Dev environment manager (formerly rtx)
- Python version management
- Task runner
- Environment variables

**ruff** - Fast Python linter and formatter (Rust-based)
- Replaces black, isort, flake8, pylint
- 10-100x faster
- Auto-fix support

**basedpyright** - Fast type checker (fork of pyright)
- Strict type checking
- LSP support for editors
- Faster than mypy

**pytest** - Testing framework
- Simple, powerful testing
- Fixtures for dependency injection
- Extensive plugin ecosystem

## pyproject.toml

Modern Python projects use `pyproject.toml` for all configuration.

### Basic Project

```toml
[project]
name = "my-project"
version = "0.1.0"
description = "My Python project"
requires-python = ">=3.12"
dependencies = [
    "fastapi>=0.100.0",
    "pydantic>=2.0.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=8.0.0",
    "ruff>=0.8.0",
    "basedpyright>=1.0.0",
]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[tool.uv]
dev-dependencies = [
    "pytest>=8.0.0",
    "ruff>=0.8.0",
    "basedpyright>=1.0.0",
]

[tool.ruff]
line-length = 100
target-version = "py312"

[tool.ruff.lint]
select = ["E", "F", "I", "N", "UP", "RUF"]

[tool.basedpyright]
typeCheckingMode = "strict"

[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
```

**Sections:**
- `[project]`: Package metadata and dependencies
- `[tool.uv]`: uv-specific configuration
- `[tool.ruff]`: Ruff linter/formatter config
- `[tool.basedpyright]`: Type checker config
- `[tool.pytest.ini_options]`: Test configuration

## uv Commands

### Project Setup

```bash
# Create new project
uv init my-project
cd my-project

# Install dependencies
uv sync

# Add dependency
uv add fastapi

# Add dev dependency
uv add --dev pytest

# Remove dependency
uv remove fastapi
```

### Running Commands

```bash
# Run Python script
uv run python script.py

# Run module
uv run python -m my_module

# Run installed tool
uv run pytest
uv run ruff check .

# Run with specific Python version
uv run --python 3.12 python script.py
```

### Dependency Management

```bash
# Update all dependencies
uv lock --upgrade

# Update specific package
uv lock --upgrade-package fastapi

# Show dependency tree
uv tree

# Export requirements.txt
uv pip compile pyproject.toml -o requirements.txt
```

See `references/uv.md` for advanced patterns.

## mise Configuration

**.mise.toml** for Python version and tasks:

```toml
[tools]
python = "3.12"

[env]
DATABASE_URL = "postgresql://localhost/mydb"
PYTHONPATH = "src"

[tasks.lint]
run = "uv run ruff check ."
description = "Run linter"

[tasks.lint-fix]
run = "uv run ruff check --fix ."
description = "Fix linting issues"

[tasks.format]
run = "uv run ruff format ."
description = "Format code"

[tasks.typecheck]
run = "uv run basedpyright"
description = "Type check"

[tasks.test]
run = "uv run pytest"
description = "Run tests"

[tasks.test-cov]
run = "uv run pytest --cov=src --cov-report=html"
description = "Run tests with coverage"

[tasks.check]
depends = ["lint", "typecheck", "test"]
description = "Run all checks"

[tasks.dev]
run = "uv run uvicorn my_app.main:app --reload"
description = "Start dev server"
```

**Usage:**
```bash
mise install          # Install Python 3.12
mise run lint         # Run linter
mise run format       # Format code
mise run test         # Run tests
mise run check        # Run all checks
mise run dev          # Start dev server
```

**Benefits:**
- Pin Python version across team
- Consistent task commands
- Environment variables
- Task dependencies

## ruff - Linting and Formatting

### Configuration

```toml
[tool.ruff]
line-length = 100
target-version = "py312"
src = ["src", "tests"]

[tool.ruff.lint]
select = [
    "E",      # pycodestyle errors
    "F",      # pyflakes
    "I",      # isort (import sorting)
    "N",      # pep8-naming
    "UP",     # pyupgrade
    "RUF",    # Ruff-specific rules
    "B",      # flake8-bugbear
    "C4",     # flake8-comprehensions
    "SIM",    # flake8-simplify
]
ignore = [
    "E501",   # Line too long (handled by formatter)
]

[tool.ruff.lint.isort]
known-first-party = ["my_app"]

[tool.ruff.format]
quote-style = "double"
indent-style = "space"
```

### Usage

```bash
# Check for issues
ruff check .

# Auto-fix issues
ruff check --fix .

# Format code
ruff format .

# Check and format
ruff check --fix . && ruff format .
```

**Features:**
- Combines black, isort, flake8, pylint
- Auto-fixes most issues
- 10-100x faster than alternatives
- Compatible with black

See `references/ruff.md` for rule details.

## basedpyright - Type Checking

### Configuration

```toml
[tool.basedpyright]
typeCheckingMode = "strict"
pythonVersion = "3.12"
include = ["src"]
exclude = ["**/__pycache__", ".venv"]
reportMissingImports = true
reportMissingTypeStubs = false
```

**Type checking modes:**
- `"off"`: No type checking
- `"basic"`: Basic type checking
- `"standard"`: Standard type checking
- `"strict"`: Strict type checking (recommended)

### Usage

```bash
# Type check entire project
basedpyright

# Type check specific file
basedpyright src/my_app/main.py

# Type check with verbose output
basedpyright --verbose
```

### Type Hints

```python
from typing import Optional
from decimal import Decimal

def calculate_discount(total: Decimal, rate: Optional[Decimal] = None) -> Decimal:
    """Type hints for function parameters and return"""
    if rate is None:
        rate = Decimal("0.1")
    return total * rate

# Type hints for variables
price: Decimal = Decimal("99.99")
items: list[str] = ["apple", "banana"]
config: dict[str, int] = {"timeout": 30}
```

See `references/basedpyright.md` for strict typing patterns.

## pytest - Testing

### Configuration

```toml
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py", "*_test.py"]
python_classes = ["Test*"]
python_functions = ["test_*"]
addopts = [
    "-ra",              # Show summary of all test outcomes
    "--strict-markers", # Error on unknown markers
    "--strict-config",  # Error on unknown config
    "-v",               # Verbose
]
markers = [
    "slow: marks tests as slow",
    "integration: marks tests as integration tests",
]
```

### Basic Tests

```python
import pytest
from decimal import Decimal
from my_app.entities import Product

def test_product_creation():
    """Basic test"""
    product = Product(name="Widget", price=Decimal("9.99"))
    assert product.name == "Widget"
    assert product.price == Decimal("9.99")

def test_product_validation():
    """Test validation"""
    with pytest.raises(ValueError, match="Price must be positive"):
        Product(name="Widget", price=Decimal("-1"))
```

### Usage

```bash
# Run all tests
pytest

# Run specific file
pytest tests/test_product.py

# Run specific test
pytest tests/test_product.py::test_product_creation

# Run with coverage
pytest --cov=src --cov-report=html

# Run only fast tests
pytest -m "not slow"

# Run with verbose output
pytest -v

# Run and stop on first failure
pytest -x
```

## Project Scripts

Define scripts in `pyproject.toml`:

```toml
[project.scripts]
my-app = "my_app.main:run"
my-worker = "my_app.worker:main"

[tool.uv.scripts]
dev = "uvicorn my_app.main:app --reload"
migrate = "alembic upgrade head"
```

**Usage:**

```bash
# Project scripts (after install)
my-app

# uv scripts (without install)
uv run dev
uv run migrate
```

## CI/CD Configuration

**GitHub Actions (.github/workflows/ci.yml):**

```yaml
name: CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up mise
        uses: jdx/mise-action@v2

      - name: Install dependencies
        run: uv sync

      - name: Lint
        run: mise run lint

      - name: Format check
        run: uv run ruff format --check .

      - name: Type check
        run: mise run typecheck

      - name: Test
        run: mise run test
```

**Key points:**
- Use mise for Python version
- Run all checks (lint, format, typecheck, test)
- Fast CI with uv and ruff

## Development Workflow

### 1. Project Setup

```bash
# Install mise
curl https://mise.run | sh

# Install Python version
mise install

# Install dependencies
uv sync
```

### 2. Make Changes

```python
# Write code with type hints
def calculate_total(items: list[Decimal]) -> Decimal:
    return sum(items)
```

### 3. Run Checks

```bash
# Format code
mise run format

# Fix linting issues
mise run lint-fix

# Type check
mise run typecheck

# Run tests
mise run test
```

### 4. Pre-commit

```bash
# Run all checks before commit
mise run check

# Or individual checks
mise run lint
mise run typecheck
mise run test
```

## Best Practices

1. **Use uv for all package management**: Faster and more reliable than pip
2. **Pin Python version with mise**: Ensures consistency across team
3. **Enable strict type checking**: Catch bugs early with basedpyright
4. **Auto-fix with ruff**: Fix most issues automatically
5. **Run checks before commit**: Use mise task dependencies
6. **Configure tools in pyproject.toml**: Single source of configuration
7. **Use mise tasks**: Consistent commands across team

## Anti-Patterns

❌ **Using pip instead of uv**: Slower, less reliable
❌ **No type hints**: Missing type safety benefits
❌ **Disabling strict typing**: Defeats purpose of type checking
❌ **Not formatting code**: Inconsistent style
❌ **Skipping tests**: Bugs in production
❌ **Manual dependency updates**: Use `uv lock --upgrade`

## Tool Comparison

| Tool | Replaces | Speed | Purpose |
|------|----------|-------|---------|
| uv | pip, pip-tools, virtualenv | 10-100x faster | Package management |
| mise | pyenv, asdf, direnv | Fast | Version management, tasks |
| ruff | black, isort, flake8, pylint | 10-100x faster | Linting, formatting |
| basedpyright | mypy | Faster | Type checking |
| pytest | unittest | - | Testing |

## Summary

**Essential tools:**
- **uv**: Package management and dependency resolution
- **mise**: Python version and task orchestration
- **ruff**: Linting and formatting
- **basedpyright**: Strict type checking
- **pytest**: Testing framework

**Configuration:**
- All tools configured in `pyproject.toml`
- mise tasks in `.mise.toml`
- Single lock file with `uv.lock`

**Workflow:**
1. `mise install` - Install Python version
2. `uv sync` - Install dependencies
3. `mise run check` - Run all checks
4. Commit and push

See references/ for advanced patterns and configuration options.
