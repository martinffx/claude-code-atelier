# uv - Advanced Patterns

uv is a fast Python package manager written in Rust. Advanced usage patterns and workflows.

## Project Commands

### Initialization

```bash
# Create new project
uv init my-project
cd my-project

# Create project with specific Python version
uv init --python 3.12 my-project

# Initialize in existing directory
uv init
```

### Dependency Management

```bash
# Add production dependency
uv add fastapi

# Add with version constraint
uv add "fastapi>=0.100.0,<0.200.0"

# Add dev dependency
uv add --dev pytest

# Add optional dependency group
uv add --optional docs sphinx

# Add from git
uv add git+https://github.com/user/repo.git

# Add from git with tag/branch
uv add git+https://github.com/user/repo.git@v1.0.0

# Add local package (editable)
uv add --editable ./packages/my-package

# Remove dependency
uv remove fastapi
```

### Sync and Lock

```bash
# Install all dependencies (creates/updates lock file)
uv sync

# Install without dev dependencies
uv sync --no-dev

# Install specific groups
uv sync --only-group docs

# Update lock file without installing
uv lock

# Update all dependencies
uv lock --upgrade

# Update specific package
uv lock --upgrade-package fastapi

# Force re-lock
uv lock --force
```

## Running Commands

### Basic Execution

```bash
# Run Python script
uv run python script.py

# Run module
uv run python -m my_module

# Run installed command
uv run pytest
uv run black .

# Run with arguments
uv run pytest -v --cov
```

### Workspace Commands

```bash
# Run command in specific workspace package
uv run --package my-api python -m my_api.main

# Run script from specific package
uv run --package my-worker python worker.py
```

### Environment Control

```bash
# Run with specific Python version
uv run --python 3.12 python script.py

# Run without creating lock file
uv run --no-sync python script.py

# Run with isolated environment
uv run --isolated pytest
```

## Scripts in pyproject.toml

### Project Scripts

```toml
[project.scripts]
my-app = "my_app.main:run"
my-cli = "my_app.cli:main"
```

**Usage:**
```bash
# After installation
my-app
my-cli --help
```

### uv Scripts

```toml
[tool.uv.scripts]
dev = "uvicorn my_app.main:app --reload"
worker = "python -m my_app.worker"
migrate = "alembic upgrade head"
shell = "python -m IPython"
```

**Usage:**
```bash
# Run without installation
uv run dev
uv run worker
uv run migrate
uv run shell
```

### Complex Scripts

```toml
[tool.uv.scripts]
# Multi-step script
setup = [
    "alembic upgrade head",
    "python -m my_app.seed_data",
]

# Script with environment
test-integration = { cmd = "pytest tests/integration", env = { DATABASE_URL = "postgresql://localhost/test" } }

# Script with working directory
build-docs = { cmd = "sphinx-build -b html source build", cwd = "docs" }
```

## pip Interface

uv provides drop-in replacement for pip commands:

```bash
# Install package
uv pip install fastapi

# Install from requirements.txt
uv pip install -r requirements.txt

# Install in target directory
uv pip install --target ./lib fastapi

# Uninstall package
uv pip uninstall fastapi

# List installed packages
uv pip list

# Show package info
uv pip show fastapi

# Compile requirements
uv pip compile pyproject.toml -o requirements.txt

# Compile with hashes
uv pip compile --generate-hashes pyproject.toml

# Sync from requirements.txt
uv pip sync requirements.txt
```

## Virtual Environments

### Create and Manage

```bash
# Create virtual environment
uv venv

# Create with specific Python version
uv venv --python 3.12

# Create in specific location
uv venv .venv-test

# Create with system packages
uv venv --system-site-packages
```

### Activation

```bash
# Linux/macOS
source .venv/bin/activate

# Windows
.venv\Scripts\activate

# Or use uv run (no activation needed)
uv run python script.py
```

## Workspace Management

### Root Configuration

```toml
[tool.uv.workspace]
members = [
    "apps/*",
    "packages/*",
]

exclude = [
    "packages/experimental",
]

[tool.uv]
dev-dependencies = [
    "pytest>=8.0.0",
    "ruff>=0.8.0",
]
```

### Workspace Commands

```bash
# Sync entire workspace
uv sync

# Sync specific package
uv sync --package my-api

# Add dependency to workspace member
uv add fastapi --package my-api

# Show workspace tree
uv tree

# Lock workspace
uv lock
```

## Dependency Resolution

### Constraints

```toml
[project]
dependencies = [
    "fastapi>=0.100.0,<0.200.0",  # Range
    "pydantic~=2.0.0",             # Compatible release
    "sqlalchemy==2.0.23",          # Exact version
]
```

### Resolution Strategy

```toml
[tool.uv]
# Prefer lowest compatible versions (for libraries)
resolution = "lowest"

# Prefer highest compatible versions (default, for apps)
resolution = "highest"
```

### Conflict Resolution

```bash
# Show why package is installed
uv tree --package fastapi

# Show dependency conflicts
uv lock --verbose
```

## Index Configuration

### Custom PyPI Index

```toml
[tool.uv]
index-url = "https://custom-pypi.example.com/simple"

# Additional indexes
extra-index-url = [
    "https://another-index.example.com/simple",
]
```

### Private Packages

```bash
# Use token for private index
export UV_INDEX_URL="https://token@private-pypi.example.com/simple"

# Or in pyproject.toml with environment variable
```

## Caching

uv caches aggressively for performance:

```bash
# Clear entire cache
uv cache clean

# Show cache directory
uv cache dir

# Show cache size
uv cache size
```

## Build Backend Integration

uv works with any PEP 517 build backend:

### Hatchling (Recommended)

```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
```

### Setuptools

```toml
[build-system]
requires = ["setuptools>=68", "wheel"]
build-backend = "setuptools.build_meta"
```

### PDM Backend

```toml
[build-system]
requires = ["pdm-backend"]
build-backend = "pdm.backend"
```

## Building and Publishing

```bash
# Build distribution
uv build

# Build only wheel
uv build --wheel

# Build only sdist
uv build --sdist

# Publish to PyPI
uv publish

# Publish to custom index
uv publish --index-url https://custom-pypi.example.com
```

## Environment Variables

```bash
# Set cache directory
export UV_CACHE_DIR=/path/to/cache

# Set Python install directory
export UV_PYTHON_INSTALL_DIR=/path/to/python

# Set index URL
export UV_INDEX_URL=https://custom-index.example.com/simple

# Disable cache
export UV_NO_CACHE=1

# Set concurrent downloads
export UV_CONCURRENT_DOWNLOADS=16
```

## Advanced Patterns

### Monorepo with Shared Dev Tools

```toml
# Root pyproject.toml
[tool.uv.workspace]
members = ["apps/*", "packages/*"]

[tool.uv]
dev-dependencies = [
    "pytest>=8.0.0",
    "ruff>=0.8.0",
    "basedpyright>=1.0.0",
]

# Individual package pyproject.toml
[project]
name = "my-api"
dependencies = ["my-core"]  # Workspace package
```

### Conditional Dependencies

```toml
[project.optional-dependencies]
postgres = ["psycopg2-binary>=2.9"]
mysql = ["pymysql>=1.0"]
all = ["psycopg2-binary>=2.9", "pymysql>=1.0"]

[tool.uv]
dev-dependencies = [
    "pytest>=8.0.0",
]
```

Install with:
```bash
uv sync --extra postgres
uv sync --extra all
```

### Pre-commit Integration

**.pre-commit-config.yaml:**

```yaml
repos:
  - repo: local
    hooks:
      - id: uv-lock
        name: Check uv lock file
        entry: uv lock --check
        language: system
        pass_filenames: false
```

### Docker with uv

```dockerfile
FROM python:3.12-slim

# Install uv
COPY --from=ghcr.io/astral-sh/uv:latest /uv /usr/local/bin/uv

WORKDIR /app

# Copy dependency files
COPY pyproject.toml uv.lock ./

# Install dependencies
RUN uv sync --frozen --no-dev

# Copy source
COPY . .

CMD ["uv", "run", "python", "-m", "my_app.main"]
```

## Performance Tips

1. **Use --frozen in CI**: Skip lock file updates
   ```bash
   uv sync --frozen
   ```

2. **Cache in CI**: Reuse uv cache between runs
   ```yaml
   - name: Cache uv
     uses: actions/cache@v4
     with:
       path: ~/.cache/uv
       key: uv-${{ hashFiles('uv.lock') }}
   ```

3. **Parallel installs**: uv parallelizes by default
   ```bash
   export UV_CONCURRENT_DOWNLOADS=32
   ```

4. **Lock file validation**: Check lock file in CI
   ```bash
   uv lock --check
   ```

## Troubleshooting

### Lock File Out of Sync

```bash
# Re-generate lock file
uv lock --force

# Check what changed
git diff uv.lock
```

### Dependency Conflicts

```bash
# Show dependency tree
uv tree

# Show why package is required
uv tree --package problematic-package

# Try with verbose output
uv sync --verbose
```

### Cache Issues

```bash
# Clear cache and reinstall
uv cache clean
uv sync --force
```

## Migration from pip

### From requirements.txt

```bash
# Create pyproject.toml from requirements.txt
uv pip compile requirements.txt --output-file pyproject.toml

# Or manually
uv add $(cat requirements.txt)
```

### From setup.py

```bash
# Convert setup.py to pyproject.toml (manual)
# Then sync
uv sync
```

## Summary

**Key commands:**
- `uv sync` - Install dependencies
- `uv add` - Add dependency
- `uv run` - Run command
- `uv lock` - Update lock file

**Advanced features:**
- Workspace support (monorepos)
- Scripts in pyproject.toml
- pip-compatible interface
- Fast dependency resolution
- Aggressive caching

**Performance:**
- 10-100x faster than pip
- Parallel downloads
- Smart caching
- Minimal network requests
