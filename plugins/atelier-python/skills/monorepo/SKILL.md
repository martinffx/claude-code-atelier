---
name: python:monorepo
description: Python monorepo architecture with uv workspaces, mise, and apps/packages pattern. Use when setting up project structure, configuring workspaces, managing dependencies across packages, or designing multi-app Python repositories.
user-invocable: false
---

# Python Monorepo with uv Workspaces

Modern Python monorepo architecture using `uv` for workspace management and `mise` for Python version and task orchestration.

## Core Concepts

**Monorepo**: Single repository containing multiple related packages and applications

**Benefits:**
- Shared code across apps
- Atomic commits across dependencies
- Consistent tooling and versions
- Simplified dependency management

**uv workspace**: Python's answer to npm/pnpm workspaces
- Single lock file for entire repo
- Shared virtual environment
- Cross-package dependency resolution

## Directory Structure

```
my-monorepo/
├── .mise.toml                    # Python version + task runner
├── pyproject.toml                # Root workspace config
├── uv.lock                       # Unified lock file
├── apps/                         # Deployable applications
│   ├── api/
│   │   ├── pyproject.toml
│   │   └── src/
│   │       └── my_api/
│   └── worker/
│       ├── pyproject.toml
│       └── src/
│           └── my_worker/
└── packages/                     # Shared libraries
    ├── core/
    │   ├── pyproject.toml
    │   └── src/
    │       └── my_core/
    └── utils/
        ├── pyproject.toml
        └── src/
            └── my_utils/
```

**apps/** - Deployable applications (APIs, workers, CLIs)
**packages/** - Shared libraries used by apps

## Root Workspace Configuration

**pyproject.toml** at root:

```toml
[project]
name = "my-monorepo"
version = "0.1.0"
requires-python = ">=3.12"

[tool.uv.workspace]
members = [
    "apps/*",
    "packages/*"
]

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
```

**Key points:**
- `tool.uv.workspace.members`: Glob patterns for workspace packages
- `dev-dependencies`: Shared dev tools (pytest, ruff, basedpyright)
- Shared tool configuration (ruff, basedpyright)

## mise Configuration

**.mise.toml** at root:

```toml
[tools]
python = "3.12"

[tasks.lint]
run = "uv run ruff check ."
description = "Run linter"

[tasks.format]
run = "uv run ruff format ."
description = "Format code"

[tasks.typecheck]
run = "uv run basedpyright"
description = "Type check"

[tasks.test]
run = "uv run pytest"
description = "Run tests"

[tasks.check]
depends = ["lint", "typecheck", "test"]
description = "Run all checks"
```

**Features:**
- `[tools]`: Pin Python version
- `[tasks]`: Define reusable tasks
- `depends`: Task dependencies

**Usage:**
```bash
mise install           # Install Python 3.12
mise run lint          # Run ruff
mise run check         # Run lint, typecheck, test
```

## Package Structure

Each package has its own `pyproject.toml`:

**packages/core/pyproject.toml:**

```toml
[project]
name = "my-core"
version = "0.1.0"
requires-python = ">=3.12"
dependencies = [
    "pydantic>=2.0.0",
]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
```

**packages/utils/pyproject.toml:**

```toml
[project]
name = "my-utils"
version = "0.1.0"
requires-python = ">=3.12"
dependencies = [
    "my-core",  # Reference workspace package
]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
```

**Key points:**
- Each package is independently versioned
- Workspace packages reference each other by name
- External dependencies listed normally

## App Structure

**apps/api/pyproject.toml:**

```toml
[project]
name = "my-api"
version = "0.1.0"
requires-python = ">=3.12"
dependencies = [
    "fastapi>=0.100.0",
    "uvicorn>=0.20.0",
    "my-core",      # Workspace package
    "my-utils",     # Workspace package
]

[project.scripts]
api = "my_api.main:run"

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
```

**apps/api/src/my_api/main.py:**

```python
from my_core.entities import User
from my_utils.logging import setup_logging

def run():
    setup_logging()
    # App code...
```

## uv Commands

### Install all workspace packages

```bash
uv sync
```

Creates unified lock file and installs all dependencies.

### Add dependency to specific package

```bash
# Add to root dev dependencies
uv add --dev pytest

# Add to specific workspace package
uv add fastapi --package my-api

# Add workspace package as dependency
uv add my-core --package my-api
```

### Run commands

```bash
# Run in root environment
uv run pytest

# Run package script
uv run --package my-api api

# Run Python module
uv run python -m my_api.main
```

### Update dependencies

```bash
# Update all packages
uv lock --upgrade

# Update specific package
uv lock --upgrade-package fastapi
```

## Namespace Packages

Use PEP 420 namespace packages for related packages:

```
packages/
├── my_company-core/
│   ├── pyproject.toml
│   └── src/
│       └── my_company/
│           └── core/
│               └── __init__.py
└── my_company-utils/
    ├── pyproject.toml
    └── src/
        └── my_company/
            └── utils/
                └── __init__.py
```

**No `__init__.py` in `my_company/`** - that's what makes it a namespace package.

**Usage:**

```python
from my_company.core import User
from my_company.utils import setup_logging
```

**pyproject.toml:**

```toml
[project]
name = "my-company-core"  # Package name (with dash)

[tool.hatch.build.targets.wheel]
packages = ["src/my_company"]  # Namespace directory
```

See `references/namespace-packages.md` for details.

## Dependency Direction

Follow strict dependency rules:

```
┌─────────────────────────────────┐
│           apps/                  │
│   (api, worker, cli)             │
│                                  │
│   Depend on ↓                    │
└─────────────────────────────────┘

┌─────────────────────────────────┐
│         packages/                │
│   (core, utils, shared)          │
│                                  │
│   No dependencies on apps ↑      │
└─────────────────────────────────┘
```

**Rules:**
1. Apps can depend on packages
2. Packages can depend on other packages
3. Packages NEVER depend on apps
4. Avoid circular dependencies between packages

**Example:**

```toml
# ✅ Good: app depends on packages
[project]
name = "my-api"
dependencies = [
    "my-core",
    "my-utils",
]

# ✅ Good: package depends on package
[project]
name = "my-utils"
dependencies = [
    "my-core",
]

# ❌ Bad: package depends on app
[project]
name = "my-core"
dependencies = [
    "my-api",  # NEVER do this
]

# ❌ Bad: circular dependency
# my-core depends on my-utils
# my-utils depends on my-core
```

## Docker Builds

Multi-stage builds for workspace packages:

**apps/api/Dockerfile:**

```dockerfile
FROM python:3.12-slim AS base

# Install uv
COPY --from=ghcr.io/astral-sh/uv:latest /uv /usr/local/bin/uv

# Set working directory
WORKDIR /app

# Copy workspace files
COPY pyproject.toml uv.lock ./
COPY packages/ ./packages/
COPY apps/api/ ./apps/api/

# Install dependencies (no dev)
RUN uv sync --frozen --no-dev --package my-api

# Runtime stage
FROM python:3.12-slim

WORKDIR /app

# Copy installed environment
COPY --from=base /app/.venv /app/.venv

# Copy source
COPY --from=base /app/apps/api /app/apps/api
COPY --from=base /app/packages /app/packages

# Set Python path
ENV PATH="/app/.venv/bin:$PATH"

# Run app
CMD ["python", "-m", "my_api.main"]
```

**Key points:**
- Multi-stage build (smaller final image)
- Copy all workspace packages (for dependencies)
- Use `uv sync --frozen --no-dev --package <name>`
- Copy .venv from build stage

See `references/docker.md` for advanced patterns.

## Testing in Monorepo

### Run all tests

```bash
uv run pytest
```

### Run tests for specific package

```bash
uv run pytest packages/core/tests
```

### Run app tests

```bash
uv run pytest apps/api/tests
```

### Test configuration

**Root pyproject.toml:**

```toml
[tool.pytest.ini_options]
testpaths = ["packages", "apps"]
python_files = ["test_*.py", "*_test.py"]
python_classes = ["Test*"]
python_functions = ["test_*"]
```

## Task Organization with mise

**Group tasks by category:**

```toml
[tasks.test]
run = "uv run pytest"

[tasks.test-core]
run = "uv run pytest packages/core"

[tasks.test-api]
run = "uv run pytest apps/api"

[tasks.lint]
run = "uv run ruff check ."

[tasks.lint-fix]
run = "uv run ruff check --fix ."

[tasks.format]
run = "uv run ruff format ."

[tasks.typecheck]
run = "uv run basedpyright"

[tasks.dev-api]
run = "uv run --package my-api uvicorn my_api.main:app --reload"
description = "Run API in dev mode"

[tasks.dev-worker]
run = "uv run --package my-worker python -m my_worker.main"
description = "Run worker in dev mode"
```

## Best Practices

1. **Single lock file**: One `uv.lock` at root for entire repo
2. **Shared tooling**: ruff, basedpyright, pytest in root dev-dependencies
3. **Consistent Python version**: Use mise to pin Python version
4. **Clear dependency direction**: Apps depend on packages, not vice versa
5. **Namespace packages**: Group related packages under namespace
6. **Task automation**: Use mise tasks for common operations
7. **Docker awareness**: Structure for efficient multi-stage builds

## Anti-Patterns

❌ **Package depends on app**: Breaks dependency hierarchy
❌ **Circular dependencies**: Package A depends on B, B depends on A
❌ **Multiple lock files**: One workspace should have one lock file
❌ **Inconsistent Python versions**: Pin Python version in mise
❌ **Duplicated tool config**: Share ruff/basedpyright config in root

## Migration from Single Package

1. Create root workspace structure
2. Move existing package to `apps/my-app` or `packages/my-package`
3. Extract shared code to `packages/core`
4. Update `pyproject.toml` with workspace config
5. Run `uv sync` to generate lock file
6. Update imports to use workspace packages

## Summary

- **uv workspace**: Manages multiple packages with single lock file
- **mise**: Pins Python version and provides task runner
- **apps/**: Deployable applications
- **packages/**: Shared libraries
- **Dependency direction**: Apps → packages, never reverse
- **Namespace packages**: Group related packages
- **Docker**: Multi-stage builds with workspace awareness

See references/ for Docker build strategies and namespace package patterns.
