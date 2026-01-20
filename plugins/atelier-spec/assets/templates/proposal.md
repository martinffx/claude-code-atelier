# Change Proposal: {{CHANGE_NAME}}

**Feature:** {{FEATURE_NAME}}
**Date:** {{DATE}}
**Status:** {{STATUS}}

---

## Motivation

### Problem Statement

{{PROBLEM_DESCRIPTION}}

### Why Now?

{{URGENCY_REASONING}}

---

## Impact Analysis

### Affected Components

| Layer | Component | Impact |
|-------|-----------|--------|
| Entity | {{ENTITY_NAME}} | {{IMPACT_DESCRIPTION}} |
| Service | {{SERVICE_NAME}} | {{IMPACT_DESCRIPTION}} |
| Repository | {{REPO_NAME}} | {{IMPACT_DESCRIPTION}} |
| Router | {{ROUTE_PATH}} | {{IMPACT_DESCRIPTION}} |

### Breaking Changes

- [ ] API contract changes
- [ ] Database schema changes
- [ ] Event payload changes
- [ ] Configuration changes

**Details:**
{{BREAKING_CHANGE_DETAILS}}

### Dependencies

- **Blocks:** {{BLOCKING_FEATURES}}
- **Blocked by:** {{BLOCKED_BY_FEATURES}}

---

## Implementation Approach

### Strategy

{{IMPLEMENTATION_STRATEGY}}

### Migration Plan (if applicable)

1. {{MIGRATION_STEP_1}}
2. {{MIGRATION_STEP_2}}
3. {{MIGRATION_STEP_3}}

### Rollback Plan

{{ROLLBACK_STRATEGY}}

---

## Risk Assessment

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| {{RISK_1}} | {{LIKELIHOOD}} | {{IMPACT}} | {{MITIGATION}} |

---

## Success Criteria

- [ ] {{SUCCESS_CRITERION_1}}
- [ ] {{SUCCESS_CRITERION_2}}
- [ ] {{SUCCESS_CRITERION_3}}

---

## References

- Current spec: `docs/spec/{{FEATURE_NAME}}/spec.md`
- Delta: `docs/changes/{{FEATURE_NAME}}/{{CHANGE_NAME}}/delta.md`
- Beads Epic: {{EPIC_ID}}
