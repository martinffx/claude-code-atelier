# ruff - Linting and Formatting

ruff is an extremely fast Python linter and formatter, written in Rust. It replaces black, isort, flake8, and pylint.

## Configuration

### Basic Setup

```toml
[tool.ruff]
# Line length
line-length = 100

# Python version target
target-version = "py312"

# Source directories
src = ["src", "tests"]

# Exclude patterns
exclude = [
    ".git",
    ".venv",
    "__pycache__",
    "*.egg-info",
    "build",
    "dist",
]
```

### Linting Rules

```toml
[tool.ruff.lint]
# Enable rules by category
select = [
    "E",      # pycodestyle errors
    "W",      # pycodestyle warnings
    "F",      # pyflakes
    "I",      # isort (import sorting)
    "N",      # pep8-naming
    "UP",     # pyupgrade
    "RUF",    # Ruff-specific rules
    "B",      # flake8-bugbear
    "C4",     # flake8-comprehensions
    "SIM",    # flake8-simplify
    "TCH",    # flake8-type-checking
    "PTH",    # flake8-use-pathlib
]

# Disable specific rules
ignore = [
    "E501",   # Line too long (handled by formatter)
    "B008",   # Function calls in default arguments
]

# Allow autofix for specific rules
fixable = ["ALL"]
unfixable = []

# Allow unused variables prefixed with underscore
dummy-variable-rgx = "^(_+|(_+[a-zA-Z0-9_]*[a-zA-Z0-9]+?))$"
```

### Import Sorting (isort)

```toml
[tool.ruff.lint.isort]
known-first-party = ["my_app"]
known-third-party = ["fastapi", "pydantic"]
section-order = ["future", "standard-library", "third-party", "first-party", "local-folder"]

# Combine as imports
combine-as-imports = true

# Force single line imports
force-single-line = false

# Lines after imports
lines-after-imports = 2
```

### Formatter

```toml
[tool.ruff.format]
# Quote style
quote-style = "double"  # or "single"

# Indent style
indent-style = "space"  # or "tab"

# Skip magic trailing comma
skip-magic-trailing-comma = false

# Line endings
line-ending = "auto"  # or "lf", "cr-lf"
```

## Rule Categories

### pycodestyle (E, W)

Code style checks:

```python
# E701: Multiple statements on one line
if True: pass  # ❌

if True:  # ✅
    pass

# E711: Comparison to None
if x == None:  # ❌
if x is None:  # ✅

# W291: Trailing whitespace
x = 1  # ❌ (trailing space)
x = 1  # ✅
```

### pyflakes (F)

Logical errors:

```python
# F401: Unused import
from os import path  # ❌ (if unused)

# F841: Unused variable
x = calculate()  # ❌ (if x never used)

# F821: Undefined name
print(undefined_var)  # ❌
```

### isort (I)

Import sorting:

```python
# ❌ Wrong order
import my_app
from fastapi import FastAPI
import os

# ✅ Correct order
import os

from fastapi import FastAPI

import my_app
```

### pep8-naming (N)

Naming conventions:

```python
# N801: Class names should use CapWords
class my_class:  # ❌
class MyClass:  # ✅

# N802: Function names should be lowercase
def MyFunction():  # ❌
def my_function():  # ✅

# N806: Variables in functions should be lowercase
def foo():
    MyVar = 1  # ❌
    my_var = 1  # ✅
```

### pyupgrade (UP)

Modern Python syntax:

```python
# UP006: Use list instead of List from typing
from typing import List  # ❌
items: List[str] = []

items: list[str] = []  # ✅ (Python 3.9+)

# UP007: Use X | Y instead of Optional[X]
from typing import Optional  # ❌
def foo(x: Optional[int]):

def foo(x: int | None):  # ✅ (Python 3.10+)

# UP032: Use f-string instead of format
name = "world"
msg = "Hello {}".format(name)  # ❌
msg = f"Hello {name}"  # ✅
```

### flake8-bugbear (B)

Common bugs:

```python
# B006: Mutable default argument
def foo(items=[]):  # ❌
    items.append(1)

def foo(items=None):  # ✅
    if items is None:
        items = []
    items.append(1)

# B007: Unused loop variable
for i in range(10):  # ❌ (i unused)
    do_something()

for _ in range(10):  # ✅
    do_something()

# B009: Do not call getattr with constant attribute
getattr(obj, "attribute")  # ❌
obj.attribute  # ✅
```

### flake8-comprehensions (C4)

Comprehension improvements:

```python
# C400: Use list comprehension
list(x for x in items)  # ❌
[x for x in items]  # ✅

# C401: Use set comprehension
set(x for x in items)  # ❌
{x for x in items}  # ✅

# C414: Unnecessary list call
sorted(list(items))  # ❌
sorted(items)  # ✅
```

### flake8-simplify (SIM)

Simplifications:

```python
# SIM105: Use contextlib.suppress
try:  # ❌
    do_something()
except ValueError:
    pass

from contextlib import suppress  # ✅
with suppress(ValueError):
    do_something()

# SIM108: Use ternary operator
if condition:  # ❌
    x = 1
else:
    x = 2

x = 1 if condition else 2  # ✅

# SIM118: Use key in dict
if "key" in dict.keys():  # ❌
if "key" in dict:  # ✅
```

### Ruff-specific (RUF)

Ruff-only rules:

```python
# RUF100: Unused noqa directive
x = 1  # noqa: F401  # ❌ (F401 not triggered)

# RUF005: Use unpacking instead of concatenation
[1] + list(items)  # ❌
[1, *items]  # ✅
```

## Usage

### Linting

```bash
# Check all files
ruff check .

# Check specific file
ruff check src/main.py

# Check with auto-fix
ruff check --fix .

# Show fixable issues
ruff check --fix --show-fixes .

# Check without fixes (diff mode)
ruff check --diff .

# Statistics
ruff check --statistics .

# Watch mode (recheck on changes)
ruff check --watch .
```

### Formatting

```bash
# Format all files
ruff format .

# Format specific file
ruff format src/main.py

# Check formatting (don't modify)
ruff format --check .

# Show diff
ruff format --diff .
```

### Combined Workflow

```bash
# Check and fix, then format
ruff check --fix . && ruff format .

# Or use mise task
[tasks.fix]
run = "ruff check --fix . && ruff format ."
```

## Per-File Ignores

Ignore rules for specific files or patterns:

```toml
[tool.ruff.lint.per-file-ignores]
"__init__.py" = ["F401"]  # Ignore unused imports in __init__.py
"tests/*" = ["S101"]      # Ignore assert usage in tests
"**/migrations/*" = ["E501"]  # Ignore line length in migrations
```

## Inline Ignores

### Single Line

```python
# Ignore specific rule
from typing import List  # noqa: UP006

# Ignore all rules
x = 1  # noqa

# Ignore multiple rules
x = 1  # noqa: F401, F841
```

### Block

```python
# ruff: noqa: F401
from os import path
from sys import argv
# ... more imports
```

### File-level

```python
# ruff: noqa
# Entire file ignored
```

## Editor Integration

### VS Code

**.vscode/settings.json:**

```json
{
  "[python]": {
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
      "source.fixAll": true,
      "source.organizeImports": true
    },
    "editor.defaultFormatter": "charliermarsh.ruff"
  }
}
```

### Pre-commit

**.pre-commit-config.yaml:**

```yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.8.0
    hooks:
      # Linter
      - id: ruff
        args: [--fix]
      # Formatter
      - id: ruff-format
```

## CI Integration

**GitHub Actions:**

```yaml
- name: Lint with ruff
  run: |
    uv run ruff check .
    uv run ruff format --check .
```

## Strict Configuration

Maximum strictness for high-quality codebases:

```toml
[tool.ruff]
line-length = 100
target-version = "py312"

[tool.ruff.lint]
select = ["ALL"]  # Enable all rules

ignore = [
    "E501",    # Line too long (formatter handles)
    "D",       # pydocstyle (if not using docstrings)
    "ANN",     # flake8-annotations (use basedpyright instead)
    "COM812",  # Trailing comma (conflicts with formatter)
    "ISC001",  # Implicit string concatenation (conflicts with formatter)
]

[tool.ruff.lint.pydocstyle]
convention = "google"  # or "numpy", "pep257"
```

## Migration from Other Tools

### From black

ruff format is compatible with black:

```toml
# Remove black config, add ruff
[tool.ruff.format]
quote-style = "double"
indent-style = "space"
```

### From isort

```toml
# Replace isort config with ruff
[tool.ruff.lint.isort]
known-first-party = ["my_app"]
combine-as-imports = true
```

### From flake8

```toml
# Replace flake8 rules with ruff
[tool.ruff.lint]
select = ["E", "F", "W", "I", "N"]
ignore = ["E501"]

[tool.ruff.lint.per-file-ignores]
"__init__.py" = ["F401"]
```

### From pylint

Most pylint rules have ruff equivalents:

```toml
[tool.ruff.lint]
select = [
    "E",    # pycodestyle
    "F",    # pyflakes
    "PL",   # pylint
    "RUF",  # ruff
]
```

## Performance

ruff is 10-100x faster than alternatives:

```bash
# Time comparison
time black .        # ~10s
time ruff format .  # ~0.1s

time flake8 .       # ~5s
time ruff check .   # ~0.05s
```

**Why so fast:**
- Written in Rust
- Parallel processing
- Smart caching
- Minimal dependencies

## Best Practices

1. **Enable auto-fix**: Most issues can be fixed automatically
   ```bash
   ruff check --fix .
   ```

2. **Use strict mode in new projects**: Start with ALL rules
   ```toml
   select = ["ALL"]
   ```

3. **Format after fixing**: Linter first, formatter second
   ```bash
   ruff check --fix . && ruff format .
   ```

4. **Per-file ignores for special cases**: Don't ignore globally
   ```toml
   [tool.ruff.lint.per-file-ignores]
   "tests/*" = ["S101"]
   ```

5. **Watch mode in development**: Auto-fix on save
   ```bash
   ruff check --watch --fix .
   ```

## Summary

**Key features:**
- Combines black, isort, flake8, pylint
- 10-100x faster
- Auto-fix most issues
- Compatible with existing tools

**Configuration:**
- All in `pyproject.toml`
- Enable rules by category
- Per-file ignores
- Formatter settings

**Workflow:**
1. `ruff check --fix .` - Fix linting issues
2. `ruff format .` - Format code
3. Commit clean code
