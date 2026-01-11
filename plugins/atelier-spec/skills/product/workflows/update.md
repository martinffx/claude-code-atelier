# Update Standards & Product Documentation

## Step 1: Check for SDD v1 Format (Migration)

@context detect old SDD format and offer migration.

Check for SDD v1 indicators:
```bash
# Check if any feature has old format files
find docs/spec -name "design.md" -o -name "tasks.md" -o -name "requirements.json" -o -name "context.json" -o -name "plan.json"
```

If old format files found:

**Prompt user:**
```
Detected SDD v1 format in {{feature_count}} features.
Migrate to SDD v2? (unified spec, Beads task tracking)

Migration will:
- Merge spec.md + design.md → unified spec.md
- Import tasks from tasks.md → Beads
- Delete old format files (design.md, tasks.md, *.json)
- Update .gitignore for Beads

Continue? [y/n]
```

## Step 1.1: Install/Initialize Beads (if migrating)

Check Beads:
```bash
bd --version
```

If not found:
```
ERROR: Beads required for SDD v2.
Install: npm install -g @beads/bd
Then run: bd init
Then retry: /product update
```

If found but not initialized:
```bash
bd init
```

## Step 1.2: Migrate Each Feature (if migrating)

@scaffold for each directory in `docs/spec/*/`:

**a) Merge spec.md + design.md:**

Read files:
- `docs/spec/<feature>/spec.md` (requirements)
- `docs/spec/<feature>/design.md` (technical design)

Create new unified spec:
- Requirements section (from spec.md)
- Technical Design section (from design.md)
- Implementation Notes section (empty)

Write to `docs/spec/<feature>/spec.md`

**b) Import tasks to Beads:**

Read `docs/spec/<feature>/tasks.md`

Parse task structure and create Beads:
```bash
# Create epic for feature
bd create "Feature: <feature>" -t epic -p 1 -l feature,<feature>

# For each open task in tasks.md
bd create "<task-description>" -p <priority> -l <feature>

# Set dependencies (if detectable from task structure)
bd dep add <child-id> <parent-id> --type blocks
```

**c) Delete old format files:**
```bash
rm docs/spec/<feature>/design.md
rm docs/spec/<feature>/tasks.md
rm docs/spec/<feature>/requirements.json
rm docs/spec/<feature>/context.json
rm docs/spec/<feature>/plan.json
```

## Step 1.3: Update .gitignore (if migrating)

Add Beads cache to `.gitignore`:
```
# Beads (SDD v2)
.beads/beads.db
.beads/beads.db-*
.beads/bd.sock
```

Note: `.beads/beads.jsonl` should be committed (it's the issue data).

## Step 2: Explore Repository Structure

@context analyze repository structure to understand the project.

Explore:
- Root configuration files (Cargo.toml, package.json, pyproject.toml, go.mod, etc.)
- Source code organization patterns
- Existing documentation structure
- Build and dependency management files

## Step 3: Detect Technology Stack (Agnostic)

@context analyze project to detect technology stack without assumptions.

Detection Strategy:
- **Rust**: Look for `Cargo.toml`
- **Node.js/TypeScript/React**: Look for `package.json`
- **Python**: Look for `pyproject.toml`, `requirements.txt`, `setup.py`
- **Go**: Look for `go.mod`
- **Java**: Look for `pom.xml`, `build.gradle`
- **C#**: Look for `.csproj`, `packages.config`
- **Ruby**: Look for `Gemfile`
- **PHP**: Look for `composer.json`
- **Other**: Analyze file patterns and directory structure

Stack Analysis:
- Primary programming language
- Web framework (if any)
- Database technology (if detectable)
- Testing framework (if detectable)
- Build system and package manager

## Step 4: Create/Update docs/standards/

@scaffold create or update standards documentation using current best practices.

### Template Selection Strategy

1. **Exact Match**: If template exists for detected stack → Use directly
2. **Similar Match**: If template exists for same language, different framework → Adapt framework-specific parts
3. **Language Match**: If template exists for language but no stack match → Use language patterns, add stack-specific sections
4. **No Match**: Create generic standards based on language-agnostic principles

### Core Standards (Always Create/Update in Project)

Copy/adapt from templates to project:
- `docs/standards/coding.md` - TDD implementation patterns and coding principles
- `docs/standards/architecture.md` - Architecture patterns and design principles

### Language-Agnostic Principles Preserved

- Layered architecture (Router → Service → Repository → Entity → Database)
- Stub-driven TDD workflow (Stub → Test → Implement → Refactor)
- Domain-driven design patterns
- Dependency injection principles
- Error handling patterns

## Step 5: Create/Update docs/product/

@scaffold create or update product documentation.

### Product Documentation Files

- `docs/product/product.md` - Product context and business requirements
- `docs/product/roadmap.md` - Development roadmap and priorities

### Content Updates

- Refresh with latest product documentation patterns
- Ensure business context is captured effectively
- Update roadmap templates with current best practices
- Maintain lightweight, implementation-focused documentation

## Step 6: Validate Standards Application

@context validate that standards are properly applied and complete.

Validation Checks:
- All required standards files exist
- Language-specific standards match detected stack
- Generic standards are present and up-to-date
- Product documentation follows latest patterns
- Cross-references between files are correct
- Template adaptations preserve core principles

## Standards & Product Documentation Updated

### Summary

- Technology stack detected agnostically
- Standards documentation created/updated from templates
- Product documentation refreshed with latest patterns
- Template adaptations preserve language-agnostic principles

### Update Results

- **Stack Detection**: Technology identified without hardcoded assumptions
- **Standards Creation**: Language-specific and generic standards applied
- **Template Adaptation**: Existing templates adapted for detected stack
- **Product Documentation**: Business context and roadmap updated

### Next Actions

1. Review updated standards documentation
2. Continue development: `/spec work [feature-name]`
3. Check product status: `/product progress`

### Standards Applied

- **Architecture**: Layered architecture patterns for detected stack
- **Coding**: Stub-driven TDD approach and language-specific conventions
- **Testing**: Unit and integration test strategies
- **Documentation**: Product context and roadmap management
