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

**Documentation Questions:**
- What is this feature supposed to do? (user story)
- What are the success criteria? (acceptance criteria)
- What business rules are enforced? (validation, constraints)
- What's missing from the current implementation?
- What should change?

[Wait for responses]

## Step 5: Load Project Context

@context retrieve relevant project information.

Read files:
- `docs/product/product.md` → vision
- `docs/spec/*/spec.md` → existing features
- `docs/standards/` → architectural patterns

Extract context on:
- Product vision alignment
- Existing related features
- Architecture patterns to follow
- Technology stack conventions

## Step 6: Generate Technical Design

@architect create technical design following project standards.

Read standards:
- `docs/standards/tech.md` → layered patterns
- `docs/standards/coding.md` → implementation guidelines

Apply architectural patterns based on requirements:

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

## Step 7: Create Specification Document

@scaffold generate unified spec.md with requirements and design.

Create `docs/spec/$ARGUMENTS/spec.md` containing:

1. **Requirements Section:**
   - User story (structured format)
   - Acceptance criteria (testable)
   - Business rules
   - Scope (included/excluded)

2. **Technical Design Section:**
   - Architecture pattern selected
   - Domain model (entities with methods)
   - Services (business operations)
   - Data persistence (if needed)
   - API endpoints (if needed)
   - Events (if applicable)

3. **Implementation Notes Section:**
   - Empty, filled during implementation

Apply template from `${CLAUDE_PLUGIN_ROOT}/assets/templates/spec.md`

## Step 8: Create Beads Epic with Contextual Tasks

@architect analyze design and create dependency-aware tasks.

Based on components in Technical Design section, create tasks for **only the layers that exist**:

Example full-stack:
```bash
bd create "Feature: $ARGUMENTS" -t epic -p 1 -l feature,$ARGUMENTS
bd create "$ARGUMENTS entity + validation" -p 1 -l $ARGUMENTS
bd create "$ARGUMENTS repository" -p 1 -l $ARGUMENTS
bd create "$ARGUMENTS service" -p 1 -l $ARGUMENTS
bd create "$ARGUMENTS router" -p 1 -l $ARGUMENTS
bd dep add <repo-id> <entity-id> --type blocks
bd dep add <service-id> <repo-id> --type blocks
bd dep add <router-id> <service-id> --type blocks
```

Example simple change (only service + router):
```bash
bd create "Feature: $ARGUMENTS" -t epic -p 1 -l feature,$ARGUMENTS
bd create "$ARGUMENTS service methods" -p 1 -l $ARGUMENTS
bd create "$ARGUMENTS router endpoints" -p 1 -l $ARGUMENTS
bd dep add <router-id> <service-id> --type blocks
```

**Dependency order:** Entity → Repository → Service → Router (bottom-up)

Store epic ID for output.

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
