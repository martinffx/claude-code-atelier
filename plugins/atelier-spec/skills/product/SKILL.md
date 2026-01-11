---
name: atelier-product
description: Product documentation and roadmap management. Use for initializing new projects, tracking product progress, updating roadmap priorities, or refreshing standards documentation.
user-invocable: true
---

# Product Management

Manage product documentation, roadmaps, and development standards.

## Workflows

| Workflow | When to Use |
|----------|-------------|
| [init](workflows/init.md) | Initialize a new project with documentation structure |
| [progress](workflows/progress.md) | Track product status and view metrics |
| [roadmap](workflows/roadmap.md) | Update roadmap with next priorities |
| [update](workflows/update.md) | Refresh standards and product documentation |

## Routing

Analyze the request and load the appropriate workflow:
- Starting a new project → Read `workflows/init.md`
- Checking status or progress → Read `workflows/progress.md`
- Updating priorities or roadmap → Read `workflows/roadmap.md`
- Refreshing standards or documentation → Read `workflows/update.md`

## Quick Reference

### Project Structure
```
docs/
├── product/
│   ├── product.md      # Product definition and features
│   └── roadmap.md      # Development priorities
├── spec/
│   └── <feature>/      # Feature specifications
└── standards/
    ├── coding.md       # Implementation patterns
    └── architecture.md # Architecture patterns
```

### Technology Detection
Automatically detects stack from config files:
- Rust: `Cargo.toml`
- Node.js/TypeScript: `package.json`
- Python: `pyproject.toml`, `requirements.txt`
- Go: `go.mod`
- Java: `pom.xml`, `build.gradle`
