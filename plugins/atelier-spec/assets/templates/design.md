# Technical Design: {{FEATURE_NAME}}{{#if CHANGE_NAME}} - {{CHANGE_NAME}}{{/if}}

**Date:** {{DATE}}
{{#if CHANGE_NAME}}**Type:** Change Design{{else}}**Type:** Initial Design{{/if}}

---

## Architecture Pattern

{{PATTERN_NAME}}: {{PATTERN_DESCRIPTION}}

```
{{ARCHITECTURE_DIAGRAM}}
```

---

## Domain Model

### {{ENTITY_NAME}}

```typescript
interface {{ENTITY_NAME}} {
  id: string;
  {{PROPERTIES}}
}

// Entity Methods
- fromRequest(request): {{ENTITY_NAME}}
- toRecord(): {{RECORD_TYPE}}
- toResponse(): {{RESPONSE_TYPE}}
- validate(): ValidationResult
```

---

## Services

### {{SERVICE_NAME}}

```typescript
interface {{SERVICE_NAME}} {
  {{METHOD_1}}(entity: {{ENTITY}}): Promise<{{RETURN_TYPE}}>;
  {{METHOD_2}}(id: string): Promise<{{RETURN_TYPE}}>;
}
```

**Business Logic:**
- {{LOGIC_1}}
- {{LOGIC_2}}

---

## Data Persistence

### {{REPOSITORY_NAME}}

```typescript
interface {{REPOSITORY_NAME}} {
  create(record: {{RECORD_TYPE}}): Promise<{{RECORD_TYPE}}>;
  findById(id: string): Promise<{{RECORD_TYPE}} | null>;
  update(id: string, record: Partial<{{RECORD_TYPE}}>): Promise<{{RECORD_TYPE}}>;
  delete(id: string): Promise<void>;
}
```

### Database Schema

```sql
CREATE TABLE {{TABLE_NAME}} (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  {{COLUMNS}}
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

{{INDEXES}}
```

{{#if HAS_MIGRATIONS}}
### Migrations

```sql
{{MIGRATION_STATEMENTS}}
```
{{/if}}

---

## API Endpoints

| Method | Path | Description | Request | Response |
|--------|------|-------------|---------|----------|
| POST | /{{resource}} | Create | {{RequestType}} | {{ResponseType}} |
| GET | /{{resource}}/:id | Get by ID | - | {{ResponseType}} |
| PUT | /{{resource}}/:id | Update | {{UpdateType}} | {{ResponseType}} |
| DELETE | /{{resource}}/:id | Delete | - | 204 |

---

## Events (if applicable)

| Event | Publisher | Payload |
|-------|-----------|---------|
| {{EVENT_NAME}} | {{SERVICE_NAME}} | {{PAYLOAD_TYPE}} |

---

{{#if CHANGE_NAME}}
## Change Impact

### Layers Affected
- {{#each AFFECTED_LAYERS}}
- [ ] {{this}}
{{/each}}

### Breaking Changes
{{#if HAS_BREAKING_CHANGES}}
- {{BREAKING_CHANGE_1}}
- {{BREAKING_CHANGE_2}}
{{else}}
None
{{/if}}

### Migration Strategy

{{MIGRATION_STRATEGY}}
{{/if}}
