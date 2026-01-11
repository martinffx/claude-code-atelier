# Complete Change: $ARGUMENTS

Format: `<feature_name> <change_name>`

## Step 1: Validate Prerequisites

@context check for change proposal and Beads epic.

Check if change exists:
- If `docs/changes/$FEATURE/$CHANGE/` doesn't exist → ERROR: "Change proposal not found. Create with: /change propose $FEATURE $CHANGE"

Check Beads epic:
```bash
bd list --label $FEATURE,$CHANGE --json
```
- Verify all tasks are closed

## Step 2: Load Change Delta

@context read delta and current spec.

Read files:
- `docs/changes/$FEATURE/$CHANGE/delta.md` → changes to merge
- `docs/spec/$FEATURE/spec.md` → current specification

Parse delta:
- ADDED: New components, methods, fields
- MODIFIED: Changed components, methods, rules
- REMOVED: Deprecated items

## Step 3: Merge Delta into Spec

@architect merge changes into unified spec.md.

Update `docs/spec/$FEATURE/spec.md`:

### Requirements Section
- Update business rules with MODIFIED rules
- Add new acceptance criteria if applicable
- Update scope if changed

### Technical Design Section
- Add ADDED components to Domain Model
- Update MODIFIED components
- Mark REMOVED components as deprecated
- Update API endpoints if changed
- Update data schema if changed

### Implementation Notes Section
- Add entry documenting the change:
  ```markdown
  ### Change: $CHANGE ({{date}})
  **Motivation**: {{from proposal}}
  **Changes**: {{summary from delta}}
  **Beads Epic**: {{epic-id}}
  ```

## Step 4: Close Beads Epic

Mark epic as complete:
```bash
bd close <epic-id> --reason "Change $CHANGE merged into spec"
```

## Step 5: Archive Change Files

Move change folder to maintain history:
```bash
git add docs/changes/$FEATURE/$CHANGE/
git add docs/spec/$FEATURE/spec.md
```

Note: Git preserves history, so change folder can be deleted after commit:
```bash
rm -rf docs/changes/$FEATURE/$CHANGE/
```

Or keep for reference (optional).

## Change Complete

Merged change into: `docs/spec/$FEATURE/spec.md`

The specification now includes:
- Updated technical design with changes
- Implementation notes documenting the change
- Closed Beads epic

**Next steps:**

1. Review updated spec: `docs/spec/$FEATURE/spec.md`
2. Commit changes: `/code commit`
3. Start next change: `/change propose $FEATURE <new_change>`
4. Or start new feature: `/spec create <new_feature>`
