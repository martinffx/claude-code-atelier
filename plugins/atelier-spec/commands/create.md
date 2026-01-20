# Initialize Feature Spec: $ARGUMENTS

## Step 1: Check Project Initialization

@context verify project has been initialized for SDD.

Check if product document exists:
```bash
test -f docs/product/product.md
```

If `docs/product/product.md` does NOT exist:
- ERROR: "Project not initialized for Spec-Driven Development."
- Instruct user: "Run `/spec:init` first to set up the project structure."
- EXIT

## Step 2: Validate Prerequisites

@context check for existing spec and Beads installation.

Check if spec already exists:
- If `docs/spec/$ARGUMENTS/spec.md` exists → ERROR: "Spec already exists. Use /spec:propose for changes."

Check Beads installation:
```bash
bd --version
```
- If command fails → ERROR: "Beads not found. Install with: npm install -g @beads/bd, then run: bd init"

## Step 3: Detect Existing Code

@context search for existing code using multiple strategies.

Search for code in these patterns (stop at first match):

1. **Feature-named directory:**
   - `src/$ARGUMENTS/`
   - `lib/$ARGUMENTS/`
   - `app/$ARGUMENTS/`
   - `packages/$ARGUMENTS/`

2. **Layer-based with feature subdirectory:**
   - `src/{routes,services,repositories,entities}/$ARGUMENTS.*`
   - `src/{routers,handlers}/$ARGUMENTS.*`

3. **Layer-based with feature in filename:**
   - `src/routes/$ARGUMENTS.{ts,py,rs,js}`
   - `src/services/$ARGUMENTS*Service.{ts,py,rs,js}`
   - `src/repositories/$ARGUMENTS*Repository.{ts,py,rs,js}`

If code found:
- Set mode: BROWNFIELD
- Analyze code structure: entities, services, routes, repositories
- Extract: methods, types, endpoints, database interactions

If no code found:
- Set mode: GREENFIELD

## Step 4: Gather Requirements

@analyst conduct structured interview based on mode.

### If GREENFIELD (no existing code):

**User Story Format:** As a [user type], I want to [action] so that [benefit]

Your user story for $ARGUMENTS:
[Wait for response]

**Acceptance Criteria (3-5 specific, testable conditions):**

Your criteria:
[Wait for response]

**Business Rules (constraints, validation rules):**

Your rules:
[Wait for response]

**Scope:**
- What's included in this feature?
- What's explicitly excluded?

[Wait for response]

### If BROWNFIELD (code exists):

Show discovered code structure to user.

**Current State Assessment:**

Reviewing the discovered code, let's assess its current state:

**What works correctly?** (preserve these)
[Wait for response]

**What's broken or buggy?** (fix these - behavior doesn't match expectations)
[Wait for response]

**What's missing entirely?** (add these - features not yet implemented)
[Wait for response]

**What should change?** (improve these - works but needs enhancement)
[Wait for response]

**Requirements Documentation:**

Now let's document the target state:

**User Story:** What should this feature do when complete?
[Wait for response]

**Acceptance Criteria:** What are the success conditions? (3-5 testable criteria)
[Wait for response]

**Business Rules:** What constraints and validation rules apply?
[Wait for response]

## Step 5-6: Load Context and Generate Design (Parallel)

<parallel>
  <agent type="clerk">
    Load product context:
    - Read `docs/product/product.md` for vision and goals
    - Read existing `docs/spec/*/spec.md` for patterns and conventions
    - Extract: product vision alignment, existing related features

    Return: product_context, existing_patterns
  </agent>

  <agent type="clerk">
    Load technical standards:
    - Read `docs/standards/tech.md` for architecture patterns
    - Read `docs/standards/coding.md` for implementation conventions
    - Extract: layered architecture patterns, technology stack conventions

    Return: architecture_patterns, coding_standards
  </agent>
</parallel>

@architect create technical design following project standards.

Apply architectural patterns based on requirements and loaded context:

**Determine needed components:**
- Entities (domain models)
- Services (business logic)
- Repositories (data persistence) - if database needed
- Routes (API endpoints) - if external exposure needed
- Events (async communication) - if event-driven
- Clients (external integrations) - if calling external APIs

**Select architecture pattern:**
- Standard CRUD API: Router → Service → Repository → Database
- External API: Router → Service → Client → External API
- Event-Driven Consumer: Consumer → Service → Repository → Database
- Event-Driven Producer: Router → Service → Producer → Event Broker
- Hybrid: Combination of above

**Design only what's needed** - contextual layer detection.

## Step 7-8: Generate Outputs (Parallel)

<parallel>
  <agent type="scaffold">
    Generate specification document:
    - Apply template from `${CLAUDE_PLUGIN_ROOT}/assets/templates/spec.md`
    - Include requirements from interview (user story, acceptance criteria, business rules, scope)
    - Include technical design from architect (architecture pattern, domain model, services, APIs, data persistence)
    - Create empty Implementation Notes section
    - Write to `docs/spec/$ARGUMENTS/spec.md`

    Return: spec_path
  </agent>

  <agent type="architect">
    Create Beads epic with dependency-aware tasks:
    - Analyze technical design to identify needed layers
    - Create epic: `bd create "Feature: $ARGUMENTS" -t epic -p 1 -l feature,$ARGUMENTS`
    - Create tasks for **only the layers that exist** (Entity, Repository, Service, Router, etc.)
    - Set dependencies: Entity → Repository → Service → Router (bottom-up)
    - Examples:
      - Full-stack: entity + repo + service + router with blocking dependencies
      - Simple change: only service + router with service blocking router

    Return: epic_id, task_count, layers_affected
  </agent>
</parallel>

## Specification Complete

Created: `docs/spec/$ARGUMENTS/spec.md`
Beads Epic: [epic-id]

The specification includes:
- Unified requirements and technical design
- {{mode}} development mode (code {{#if code_found}}detected and documented{{else}}designed from scratch{{/if}})
- Contextual task breakdown ({{task_count}} tasks for {{layers_affected}} layers)
- Dependency-ordered implementation plan

**Next steps:**

1. Review the generated specification
2. Begin implementation: `/spec:work $ARGUMENTS`
3. Check progress: `/spec:status $ARGUMENTS`
