# Propose Change: $ARGUMENTS

Format: `<feature_name> <change_name>`

## Step 1: Validate Prerequisites

@context check for existing spec and Beads installation.

Check if spec exists:
- If `docs/spec/$FEATURE/spec.md` doesn't exist → ERROR: "Feature spec not found. Create with: /spec create $FEATURE"

Check Beads installation:
```bash
bd --version
```
- If command fails → ERROR: "Beads not found. Install with: npm install -g @beads/bd, then run: bd init"

## Step 2: Load Current State

@context retrieve current feature specification.

Read files:
- `docs/spec/$FEATURE/spec.md` → current requirements and design
- `docs/standards/` → architectural patterns

Extract current state:
- Existing entities, services, repositories, routes
- Current business rules and constraints
- API endpoints and data models

## Step 3: Gather Change Requirements

@analyst conduct lightweight interview for change.

Show current feature implementation context.

**Change Questions:**

**What needs to change?**
[Wait for response]

**Why is this change needed?** (motivation)
[Wait for response]

**What components are affected?**
[Wait for response]

**Are there breaking changes?**
[Wait for response]

**What's the implementation approach?**
[Wait for response]

## Step 4: Generate Technical Delta

@architect analyze impact and create delta specification.

Read:
- `docs/spec/$FEATURE/spec.md` → current design
- User responses → proposed changes

Determine impact:
- Which layers need changes (Entity, Repository, Service, Router)
- New methods, fields, or components
- Modified business rules
- Breaking changes

Generate delta with ADDED/MODIFIED/REMOVED structure.

## Step 5: Create Change Documents

@scaffold create proposal and delta files.

Create directory: `docs/changes/$FEATURE/$CHANGE/`

Files to create:
1. **proposal.md** - Using template `${CLAUDE_PLUGIN_ROOT}/assets/templates/proposal.md`
   - Motivation, impact analysis, implementation approach

2. **delta.md** - Using template `${CLAUDE_PLUGIN_ROOT}/assets/templates/delta.md`
   - ADDED: New components, methods, fields
   - MODIFIED: Changed components, methods, rules
   - REMOVED: Deprecated items

## Step 6: Create Beads Epic with Contextual Tasks

@architect generate tasks for affected layers only.

Based on delta, create tasks for **only the layers that are affected**:

Example (service + router change):
```bash
bd create "Change: $FEATURE - $CHANGE" -t epic -p 1 -l $FEATURE,change
bd create "$FEATURE service: $CHANGE" -p 1 -l $FEATURE,$CHANGE
bd create "$FEATURE router: $CHANGE" -p 1 -l $FEATURE,$CHANGE
bd dep add <router-id> <service-id> --type blocks
```

Example (full-stack change):
```bash
bd create "Change: $FEATURE - $CHANGE" -t epic -p 1 -l $FEATURE,change
bd create "$FEATURE entity: $CHANGE" -p 1 -l $FEATURE,$CHANGE
bd create "$FEATURE repository: $CHANGE" -p 1 -l $FEATURE,$CHANGE
bd create "$FEATURE service: $CHANGE" -p 1 -l $FEATURE,$CHANGE
bd create "$FEATURE router: $CHANGE" -p 1 -l $FEATURE,$CHANGE
bd dep add <repo-id> <entity-id> --type blocks
bd dep add <service-id> <repo-id> --type blocks
bd dep add <router-id> <service-id> --type blocks
```

Store epic ID for output.

## Change Proposal Complete

Created:
- `docs/changes/$FEATURE/$CHANGE/proposal.md`
- `docs/changes/$FEATURE/$CHANGE/delta.md`

Beads Epic: [epic-id]

The change proposal includes:
- Impact analysis and motivation
- Technical delta (ADDED/MODIFIED/REMOVED)
- Contextual task breakdown ({{task_count}} tasks for {{layers_affected}} layers)
- Dependency-ordered implementation plan

**Next steps:**

1. Review the proposal and delta
2. Begin implementation: `/spec work $FEATURE`
3. When complete: `/change complete $FEATURE $CHANGE`
