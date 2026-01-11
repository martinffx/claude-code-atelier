---
name: drizzle-orm
description: Type-safe SQL with Drizzle ORM in TypeScript. Use when defining database schemas, writing queries, setting up relations, running migrations, or working with PostgreSQL/MySQL/SQLite data layers.
user-invocable: false
---

# Drizzle ORM

Lightweight, type-safe ORM with SQL-like and relational query APIs.

## Schema Definition (PostgreSQL)

```typescript
import {
  pgTable,
  serial,
  text,
  integer,
  timestamp,
  boolean,
  varchar,
  uuid,
  primaryKey,
  unique,
  index
} from 'drizzle-orm/pg-core'

export const users = pgTable('users', {
  id: serial('id').primaryKey(),
  name: text('name').notNull(),
  email: varchar('email', { length: 255 }).notNull().unique(),
  age: integer('age'),
  isActive: boolean('is_active').default(true),
  createdAt: timestamp('created_at').defaultNow().notNull(),
  updatedAt: timestamp('updated_at').$onUpdate(() => new Date()),
})

export const posts = pgTable('posts', {
  id: serial('id').primaryKey(),
  title: text('title').notNull(),
  content: text('content'),
  authorId: integer('author_id')
    .notNull()
    .references(() => users.id, { onDelete: 'cascade' }),
  createdAt: timestamp('created_at').defaultNow().notNull(),
})
```

## Type Inference

```typescript
// Infer types from schema - no manual interfaces needed
export type User = typeof users.$inferSelect
export type NewUser = typeof users.$inferInsert

export type Post = typeof posts.$inferSelect
export type NewPost = typeof posts.$inferInsert
```

## Relations

```typescript
import { relations } from 'drizzle-orm'

export const usersRelations = relations(users, ({ many }) => ({
  posts: many(posts),
}))

export const postsRelations = relations(posts, ({ one }) => ({
  author: one(users, {
    fields: [posts.authorId],
    references: [users.id],
  }),
}))
```

## Database Connection

```typescript
import { drizzle } from 'drizzle-orm/node-postgres'
import { Pool } from 'pg'
import * as schema from './schema'

const pool = new Pool({ connectionString: process.env.DATABASE_URL })
export const db = drizzle(pool, { schema })
```

## SQL-like Queries

```typescript
import { eq, and, or, gt, like, isNull, desc, asc } from 'drizzle-orm'

// Select all
const allUsers = await db.select().from(users)

// Select specific columns
const names = await db.select({ name: users.name }).from(users)

// Where clause
const activeUsers = await db
  .select()
  .from(users)
  .where(eq(users.isActive, true))

// Multiple conditions
const filtered = await db
  .select()
  .from(users)
  .where(and(
    eq(users.isActive, true),
    gt(users.age, 18)
  ))

// Like/pattern matching
const matching = await db
  .select()
  .from(users)
  .where(like(users.email, '%@example.com'))

// Order and limit
const recent = await db
  .select()
  .from(posts)
  .orderBy(desc(posts.createdAt))
  .limit(10)

// Joins
const postsWithAuthors = await db
  .select({
    postTitle: posts.title,
    authorName: users.name,
  })
  .from(posts)
  .leftJoin(users, eq(posts.authorId, users.id))
```

## Relational Queries

```typescript
// Requires schema with relations passed to drizzle()
const db = drizzle(pool, { schema })

// Find many with relations
const usersWithPosts = await db.query.users.findMany({
  with: {
    posts: true,
  },
})

// Partial columns + nested relations
const partial = await db.query.users.findMany({
  columns: {
    id: true,
    name: true,
  },
  with: {
    posts: {
      columns: {
        title: true,
        createdAt: true,
      },
    },
  },
})

// Find first
const user = await db.query.users.findFirst({
  where: eq(users.id, 1),
  with: { posts: true },
})

// Exclude columns
const withoutEmail = await db.query.users.findMany({
  columns: {
    email: false, // exclude
  },
})
```

## Insert

```typescript
// Single insert
const [newUser] = await db
  .insert(users)
  .values({ name: 'Alice', email: 'alice@example.com' })
  .returning()

// Multiple insert
await db.insert(users).values([
  { name: 'Bob', email: 'bob@example.com' },
  { name: 'Carol', email: 'carol@example.com' },
])

// Upsert (on conflict)
await db
  .insert(users)
  .values({ name: 'Alice', email: 'alice@example.com' })
  .onConflictDoUpdate({
    target: users.email,
    set: { name: 'Alice Updated' },
  })
```

## Update

```typescript
await db
  .update(users)
  .set({ isActive: false })
  .where(eq(users.id, 1))

// Update with returning
const [updated] = await db
  .update(users)
  .set({ name: 'New Name' })
  .where(eq(users.id, 1))
  .returning()
```

## Delete

```typescript
await db.delete(users).where(eq(users.id, 1))

// Delete with returning
const [deleted] = await db
  .delete(users)
  .where(eq(users.id, 1))
  .returning()
```

## Transactions

```typescript
await db.transaction(async (tx) => {
  const [user] = await tx
    .insert(users)
    .values({ name: 'Alice', email: 'alice@example.com' })
    .returning()

  await tx.insert(posts).values({
    title: 'First Post',
    authorId: user.id,
  })
})
```

## Migrations (drizzle-kit)

```typescript
// drizzle.config.ts
import { defineConfig } from 'drizzle-kit'

export default defineConfig({
  schema: './src/db/schema.ts',
  out: './drizzle',
  dialect: 'postgresql',
  dbCredentials: {
    url: process.env.DATABASE_URL!,
  },
})
```

```bash
# Generate migration
npx drizzle-kit generate

# Apply migrations
npx drizzle-kit migrate

# Open Drizzle Studio
npx drizzle-kit studio

# Pull schema from existing DB
npx drizzle-kit pull
```

## Column Helpers

```typescript
// UUID with auto-generation
id: uuid('id').defaultRandom().primaryKey()

// Timestamps
createdAt: timestamp('created_at').defaultNow().notNull()
updatedAt: timestamp('updated_at').$onUpdate(() => new Date())

// Enum
import { pgEnum } from 'drizzle-orm/pg-core'
export const statusEnum = pgEnum('status', ['pending', 'active', 'archived'])
// usage: status: statusEnum('status').default('pending')

// JSON
metadata: jsonb('metadata').$type<{ key: string }>()
```

## Composite Keys & Indexes

```typescript
export const postTags = pgTable('post_tags', {
  postId: integer('post_id').references(() => posts.id),
  tagId: integer('tag_id').references(() => tags.id),
}, (t) => [
  primaryKey({ columns: [t.postId, t.tagId] }),
])

export const users = pgTable('users', {
  // columns...
}, (t) => [
  unique('unique_email').on(t.email),
  index('name_idx').on(t.name),
])
```

## Guidelines

1. Define schema in dedicated `schema.ts` file(s)
2. Use `$inferSelect` and `$inferInsert` for types - don't duplicate
3. Always define relations for nested queries with `db.query`
4. Pass `{ schema }` to `drizzle()` to enable relational queries
5. Use SQL-like API (`db.select()`) for complex joins
6. Use relational API (`db.query`) for nested data fetching
7. Foreign keys need explicit `references(() => table.column)`
8. Use `returning()` to get inserted/updated/deleted rows
