---
name: atelier-change
description: Brownfield change management for existing features. Use when proposing changes to existing specifications, modifying implemented features, or completing change cycles.
user-invocable: true
---

# Change Management

Manage changes to existing features through proposal, implementation, and completion.

## Workflows

| Workflow | When to Use |
|----------|-------------|
| [propose](workflows/propose.md) | Propose changes to an existing feature |
| [complete](workflows/complete.md) | Merge delta into spec and close the change epic |

## Routing

Analyze the request and load the appropriate workflow:
- Proposing a new change or modification → Read `workflows/propose.md`
- Finishing/completing a change cycle → Read `workflows/complete.md`

## Quick Reference

### Change Flow
1. `/change propose <feature> <change>` - Create proposal + delta
2. `/spec work <feature>` - Implement the changes
3. `/change complete <feature> <change>` - Merge delta into spec

### Delta Structure
- **ADDED**: New components, methods, fields
- **MODIFIED**: Changed components, methods, rules
- **REMOVED**: Deprecated items

### Output Files
- `docs/changes/<feature>/<change>/proposal.md` - Motivation and approach
- `docs/changes/<feature>/<change>/delta.md` - Technical changes
