---
name: dynamodb-toolbox
description: DynamoDB single-table design using dynamodb-toolbox v2. Use when creating entities, defining key patterns, designing GSIs, writing queries, implementing pagination, or working with any DynamoDB data layer in TypeScript projects.
user-invocable: false
---

# DynamoDB with dynamodb-toolbox v2

Type-safe DynamoDB interactions with Entity and Table abstractions.

## Table Configuration

```typescript
import { Table } from 'dynamodb-toolbox/table'

const AppTable = new Table({
  name: process.env.TABLE_NAME || "AppTable",
  partitionKey: { name: "PK", type: "string" },
  sortKey: { name: "SK", type: "string" },
  indexes: {
    GSI1: {
      type: "global",
      partitionKey: { name: "GSI1PK", type: "string" },
      sortKey: { name: "GSI1SK", type: "string" },
    },
    GSI2: {
      type: "global",
      partitionKey: { name: "GSI2PK", type: "string" },
      sortKey: { name: "GSI2SK", type: "string" },
    },
    GSI3: {
      type: "global",
      partitionKey: { name: "GSI3PK", type: "string" },
      sortKey: { name: "GSI3SK", type: "string" },
    },
  },
  entityAttributeSavedAs: "_et", // default, customize if needed
});
```

## Index Purpose

| Index | Purpose |
|-------|---------|
| Main Table (PK/SK) | Primary entity access |
| GSI1 | General secondary access patterns |
| GSI2 | Entity-specific queries and relationships |
| GSI3 | Hierarchical queries with temporal sorting |

## Key Patterns

```
Entity:      ENTITY#{id}
Hierarchy:   PARENT#{parent_id}#CHILD#{child_id}
Prefix:      TYPE#{category}#{identifier}
Temporal:    ENTITY#{id}#{timestamp}
```

## Entity Definition (v2 syntax)

```typescript
import { Entity } from 'dynamodb-toolbox/entity'
import { item } from 'dynamodb-toolbox/schema/item'
import { string } from 'dynamodb-toolbox/schema/string'
import { number } from 'dynamodb-toolbox/schema/number'
import { prefix } from 'dynamodb-toolbox/transformers/prefix'

const UserEntity = new Entity({
  name: "USER",
  table: AppTable,
  schema: item({
    // Key attributes - use .key() and .savedAs() for mapping
    username: string().key().savedAs("PK").transform(prefix("ACCOUNT#")),
    sk: string().key().savedAs("SK").default(() => "PROFILE"),

    // GSI keys
    gsi1pk: string().savedAs("GSI1PK").optional(),
    gsi1sk: string().savedAs("GSI1SK").optional(),

    // Regular attributes
    email: string(),
    bio: string().optional(),
    createdAt: string().default(() => new Date().toISOString()),
  }),
});
```

## Alternative: Using computeKey

```typescript
const UserEntity = new Entity({
  name: "USER",
  table: AppTable,
  schema: item({
    username: string().key(),
    email: string(),
  }),
  computeKey: ({ username }) => ({
    PK: `ACCOUNT#${username}`,
    SK: "PROFILE",
  }),
});
```

## Alternative: Using link() for derived keys

```typescript
const RepoEntity = new Entity({
  name: "REPO",
  table: AppTable,
  schema: item({
    owner: string().key(),
    repoName: string().key(),
  }).and(prev => ({
    PK: string().key().link<typeof prev>(
      ({ owner, repoName }) => `REPO#${owner}#${repoName}`
    ),
    SK: string().key().link<typeof prev>(
      ({ owner, repoName }) => `REPO#${owner}#${repoName}`
    ),
  })),
});
```

## Type Safety (v2)

```typescript
import { type InputItem, type DecodedItem } from 'dynamodb-toolbox/entity'

// Input types for creating/updating
type UserInput = InputItem<typeof UserEntity>

// Output types for reading
type User = DecodedItem<typeof UserEntity>
```

## Repository Pattern (v2)

```typescript
import { EntityRepository } from 'dynamodb-toolbox/entity/actions/repository'

const userRepo = UserEntity.build(EntityRepository)

// Put
await userRepo.put({ username: "alice", email: "alice@example.com" })

// Get
const { Item } = await userRepo.get({ username: "alice" })

// Delete
await userRepo.delete({ username: "alice" })
```

## Query Commands

```typescript
import { QueryCommand } from 'dynamodb-toolbox/table/actions/query'

const { Items } = await AppTable.build(QueryCommand)
  .entities(UserEntity)
  .query({ partition: "ACCOUNT#alice" })
  .send()
```

## Pagination

```typescript
function encodePageToken(lastEvaluated?: Record<string, any>): string | undefined {
  if (!lastEvaluated) return undefined;
  return Buffer.from(JSON.stringify(lastEvaluated)).toString("base64");
}

function decodePageToken(token?: string): Record<string, any> | undefined {
  if (!token) return undefined;
  return JSON.parse(Buffer.from(token, "base64").toString());
}
```

## Guidelines

1. Use `item({})` or `map({})` for schema definition (not `schema()`)
2. Mark key attributes with `.key()`
3. Use `.savedAs()` to map to table attribute names
4. Use `.transform(prefix(...))` for key prefixes
5. Use `DecodedItem` for read types, `InputItem` for write types
6. Prefer `EntityRepository` for simple CRUD operations
7. Use `computeKey` or `.link()` for complex key derivation
