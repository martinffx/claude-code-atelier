# Delta: {{CHANGE_NAME}}

**Feature:** {{FEATURE_NAME}}
**Date:** {{DATE}}

This document describes the technical changes to be made to the {{FEATURE_NAME}} specification.

---

## ADDED

### New Components

#### {{NEW_COMPONENT_NAME}}

```typescript
// New interface/class definition
{{CODE_BLOCK}}
```

### New Methods

#### {{SERVICE_NAME}}.{{NEW_METHOD_NAME}}

```typescript
{{METHOD_NAME}}({{PARAMS}}): Promise<{{RETURN_TYPE}}>
```

**Purpose:** {{METHOD_PURPOSE}}

### New Endpoints

| Method | Path | Description | Request | Response |
|--------|------|-------------|---------|----------|
| {{METHOD}} | {{PATH}} | {{DESCRIPTION}} | {{REQUEST}} | {{RESPONSE}} |

### New Database Fields

```sql
ALTER TABLE {{TABLE_NAME}} ADD COLUMN {{COLUMN_NAME}} {{COLUMN_TYPE}};
```

### New Events

| Event | Publisher | Payload |
|-------|-----------|---------|
| {{EVENT_NAME}} | {{PUBLISHER}} | {{PAYLOAD_TYPE}} |

---

## MODIFIED

### Changed Components

#### {{COMPONENT_NAME}}

**Before:**
```typescript
{{BEFORE_CODE}}
```

**After:**
```typescript
{{AFTER_CODE}}
```

**Reason:** {{CHANGE_REASON}}

### Changed Methods

#### {{SERVICE_NAME}}.{{METHOD_NAME}}

**Before:**
```typescript
{{BEFORE_SIGNATURE}}
```

**After:**
```typescript
{{AFTER_SIGNATURE}}
```

**Changes:**
- {{CHANGE_1}}
- {{CHANGE_2}}

### Changed Endpoints

| Endpoint | Change | Before | After |
|----------|--------|--------|-------|
| {{ENDPOINT}} | {{CHANGE_TYPE}} | {{BEFORE}} | {{AFTER}} |

### Changed Business Rules

| Rule | Before | After |
|------|--------|-------|
| {{RULE_NAME}} | {{BEFORE_RULE}} | {{AFTER_RULE}} |

---

## REMOVED

### Deprecated Components

#### {{DEPRECATED_COMPONENT}}

**Reason:** {{DEPRECATION_REASON}}
**Migration:** {{MIGRATION_PATH}}

### Removed Methods

- `{{SERVICE_NAME}}.{{REMOVED_METHOD}}` - {{REASON}}

### Removed Endpoints

| Method | Path | Reason |
|--------|------|--------|
| {{METHOD}} | {{PATH}} | {{REASON}} |

### Removed Database Fields

```sql
ALTER TABLE {{TABLE_NAME}} DROP COLUMN {{COLUMN_NAME}};
```

---

## Migration Notes

### Data Migration

{{DATA_MIGRATION_STEPS}}

### Code Migration

{{CODE_MIGRATION_STEPS}}

---

## Verification Checklist

- [ ] All ADDED components implemented
- [ ] All MODIFIED components updated
- [ ] All REMOVED components deprecated/deleted
- [ ] Tests updated for changes
- [ ] Documentation updated
- [ ] Migration scripts tested
