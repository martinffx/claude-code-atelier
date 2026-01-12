# Sync Spec from Code: $ARGUMENTS

## Step 1: Validate Prerequisites

@context verify spec exists.

Check if spec exists:
- If `docs/spec/$ARGUMENTS/spec.md` does NOT exist → ERROR: "No spec found. Use /spec:create to create a spec first."

## Step 2: Find Code Location

@context locate code for feature.

**Strategy 1: From spec references**
- Read `docs/spec/$ARGUMENTS/spec.md`
- Extract file paths mentioned in Implementation Notes or design
- Use those paths as starting point

**Strategy 2: Convention-based search**
Search for code in these patterns:

1. Feature-named directory:
   - `src/$ARGUMENTS/`
   - `lib/$ARGUMENTS/`
   - `app/$ARGUMENTS/`

2. Layer-based with feature:
   - `src/{routes,services,repositories,entities}/$ARGUMENTS.*`
   - `src/routes/$ARGUMENTS.{ts,py,rs,js}`
   - `src/services/$ARGUMENTS*Service.{ts,py,rs,js}`

If no code found → WARN: "No code found for $ARGUMENTS. Spec unchanged."

## Step 3: Analyze Current Code

@architect examine code structure and extract components.

For each discovered file:

**Entities:**
- Class/interface definitions
- Methods (fromRequest, toRecord, toResponse, validate)
- Properties and types

**Services:**
- Class definitions
- Public methods (operation signatures)
- Dependencies (injected repositories, clients)

**Repositories:**
- Class definitions
- CRUD operations
- Query methods
- Database interactions

**Routes/Controllers:**
- Endpoints (method, path)
- Request/response types
- Handler logic

**Events:**
- Published events
- Subscribed events
- Event handlers

**Clients:**
- External API calls
- Integration points

Extract signatures, types, and patterns.

## Step 4: Compare Code to Spec

@architect compare discovered code against current spec.

Read `docs/spec/$ARGUMENTS/spec.md`

Identify differences:

**In code but NOT in spec:**
- New methods not documented
- New endpoints not in API section
- New entities or entity methods
- New database fields
- New events

**In spec but NOT in code:**
- Planned features not yet implemented
- Removed functionality
- Changed signatures

**Different from spec:**
- Method signatures changed
- Endpoint paths or verbs changed
- Database schema differences

## Step 5: Update Spec with Code Reality

@scaffold update spec to match actual code.

Update `docs/spec/$ARGUMENTS/spec.md`:

### Technical Design Section

**Update Domain Model:**
- Add discovered entities
- Update entity methods to match code
- Add/remove properties based on code

**Update Services:**
- Add/update service operations from code
- Match method signatures
- Document actual dependencies

**Update API Endpoints:**
- Add discovered endpoints
- Update paths, methods, request/response types
- Remove endpoints not in code

**Update Data Persistence:**
- Sync database schema with actual tables/collections
- Update indexes from code
- Match field types

**Update Events:**
- Add discovered event publications
- Add discovered event subscriptions
- Match event payload types

### Implementation Notes Section

Add sync entry:
```markdown
## Implementation Notes
- Synced from code ({{date}})
  - Discovered: {{discovered_items}}
  - Updated: {{updated_items}}
  - Removed: {{removed_items}}
```

Write updated spec back.

## Step 6: Create Beads for Incomplete Work

@context identify and track incomplete implementation.

Scan code for markers of incomplete work:

**NotImplementedError / todo!() / throw new Error('Not implemented'):**
```bash
grep -r "NotImplementedError\|throw new Error('Not implemented')\|todo!()" src/$ARGUMENTS/
```

**TODO comments:**
```bash
grep -r "TODO\|FIXME\|XXX" src/$ARGUMENTS/
```

**Unhandled edge cases:**
- Missing error handling
- Missing validation
- Incomplete test coverage

For each discovered issue:
```bash
bd create "Fix: <description>" -t task -p 2 -l $ARGUMENTS,technical-debt
```

## Sync Complete

Updated: `docs/spec/$ARGUMENTS/spec.md`

Sync results:
- Discovered {{new_count}} new elements
- Updated {{changed_count}} existing elements
- Removed {{removed_count}} outdated elements
- Created {{beads_count}} Beads for incomplete work

The spec now reflects actual code state.

**Next steps:**

1. Review synced spec: `docs/spec/$ARGUMENTS/spec.md`
2. Address incomplete work: `/spec:work $ARGUMENTS`
3. Propose new changes: `/spec:propose $ARGUMENTS <change>`
4. Check status: `/spec:status $ARGUMENTS`
