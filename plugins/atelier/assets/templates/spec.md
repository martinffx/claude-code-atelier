# {{FEATURE_NAME}} Specification

## Requirements

### User Story

As a {{USER_TYPE}}, I want to {{ACTION}} so that {{BENEFIT}}.

### Acceptance Criteria

1. {{CRITERION_1}}
2. {{CRITERION_2}}
3. {{CRITERION_3}}

### Business Rules

- {{RULE_1}}
- {{RULE_2}}

### Scope

**Included:**
- {{INCLUDED_1}}
- {{INCLUDED_2}}

**Excluded:**
- {{EXCLUDED_1}}
- {{EXCLUDED_2}}

---

## Technical Design

### Architecture Pattern

{{PATTERN_NAME}}: {{PATTERN_DESCRIPTION}}

```
{{ARCHITECTURE_DIAGRAM}}
```

### Domain Model

#### {{ENTITY_NAME}}

```typescript
interface {{ENTITY_NAME}} {
  id: string;
  // properties
}

// Entity Methods
- fromRequest(request): {{ENTITY_NAME}}
- toRecord(): {{RECORD_TYPE}}
- toResponse(): {{RESPONSE_TYPE}}
- validate(): ValidationResult
```

### Services

#### {{SERVICE_NAME}}

```typescript
interface {{SERVICE_NAME}} {
  {{METHOD_1}}(entity: {{ENTITY}}): Promise<{{RETURN_TYPE}}>;
  {{METHOD_2}}(id: string): Promise<{{RETURN_TYPE}}>;
}
```

### Data Persistence

#### {{REPOSITORY_NAME}}

```typescript
interface {{REPOSITORY_NAME}} {
  create(record: {{RECORD_TYPE}}): Promise<{{RECORD_TYPE}}>;
  findById(id: string): Promise<{{RECORD_TYPE}} | null>;
  update(id: string, record: Partial<{{RECORD_TYPE}}>): Promise<{{RECORD_TYPE}}>;
  delete(id: string): Promise<void>;
}
```

#### Database Schema

```sql
CREATE TABLE {{TABLE_NAME}} (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  -- columns
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);
```

### API Endpoints

| Method | Path | Description | Request | Response |
|--------|------|-------------|---------|----------|
| POST | /{{resource}} | Create | {{RequestType}} | {{ResponseType}} |
| GET | /{{resource}}/:id | Get by ID | - | {{ResponseType}} |
| PUT | /{{resource}}/:id | Update | {{UpdateType}} | {{ResponseType}} |
| DELETE | /{{resource}}/:id | Delete | - | 204 |

### Events (if applicable)

| Event | Publisher | Payload |
|-------|-----------|---------|
| {{EVENT_NAME}} | {{SERVICE_NAME}} | {{PAYLOAD_TYPE}} |

---

## Implementation Notes

<!-- Filled during implementation -->
