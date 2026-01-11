---
name: fastify-typebox
description: Building REST APIs with Fastify and TypeBox in TypeScript. Use when creating routes, defining request/response schemas, implementing validation, structuring API modules, or working with type-safe HTTP handlers.
user-invocable: false
---

# Fastify + TypeBox APIs

Type-safe REST APIs with automatic validation and serialization.

## Setup

```bash
npm i fastify @fastify/type-provider-typebox @sinclair/typebox
```

```typescript
import Fastify from 'fastify'
import { TypeBoxTypeProvider } from '@fastify/type-provider-typebox'

const app = Fastify({ logger: true }).withTypeProvider<TypeBoxTypeProvider>()
```

## Schema Definition

```typescript
import { Type, Static } from '@sinclair/typebox'

// Request/response schemas
export const UserSchema = Type.Object({
  id: Type.String({ format: 'uuid' }),
  name: Type.String({ minLength: 1, maxLength: 100 }),
  email: Type.String({ format: 'email' }),
  createdAt: Type.String({ format: 'date-time' }),
})

export type User = Static<typeof UserSchema>

// Input schemas (omit generated fields)
export const CreateUserSchema = Type.Object({
  name: Type.String({ minLength: 1, maxLength: 100 }),
  email: Type.String({ format: 'email' }),
})

export type CreateUserInput = Static<typeof CreateUserSchema>
```

## Route with Full Schema

```typescript
app.post('/users', {
  schema: {
    body: CreateUserSchema,
    response: {
      201: UserSchema,
      400: Type.Object({
        statusCode: Type.Number(),
        error: Type.String(),
        message: Type.String(),
      }),
    },
  },
}, async (request, reply) => {
  const { name, email } = request.body // fully typed

  const user = await createUser({ name, email })
  return reply.status(201).send(user)
})
```

## Common Schema Patterns

```typescript
// Path parameters
const ParamsSchema = Type.Object({
  id: Type.String({ format: 'uuid' }),
})

// Query string with pagination
const QuerySchema = Type.Object({
  limit: Type.Optional(Type.Integer({ minimum: 1, maximum: 100, default: 20 })),
  cursor: Type.Optional(Type.String()),
  sort: Type.Optional(Type.Union([Type.Literal('asc'), Type.Literal('desc')])),
})

// Paginated response wrapper
const PaginatedResponse = <T extends TSchema>(itemSchema: T) =>
  Type.Object({
    items: Type.Array(itemSchema),
    nextCursor: Type.Optional(Type.String()),
    hasMore: Type.Boolean(),
  })

app.get('/users/:id', {
  schema: {
    params: ParamsSchema,
    querystring: QuerySchema,
    response: {
      200: UserSchema,
      404: Type.Object({ message: Type.String() }),
    },
  },
}, async (request, reply) => {
  const { id } = request.params
  const { limit, cursor } = request.query
  // ...
})
```

## Modular Route Registration

```typescript
// types.ts - Export typed Fastify instance
import {
  FastifyInstance,
  FastifyBaseLogger,
  RawReplyDefaultExpression,
  RawRequestDefaultExpression,
  RawServerDefault,
} from 'fastify'
import { TypeBoxTypeProvider } from '@fastify/type-provider-typebox'

export type FastifyTypebox = FastifyInstance<
  RawServerDefault,
  RawRequestDefaultExpression<RawServerDefault>,
  RawReplyDefaultExpression<RawServerDefault>,
  FastifyBaseLogger,
  TypeBoxTypeProvider
>
```

```typescript
// routes/users.ts
import { Type } from '@sinclair/typebox'
import { FastifyTypebox } from '../types'

export async function userRoutes(app: FastifyTypebox) {
  app.get('/users', {
    schema: {
      response: {
        200: Type.Array(UserSchema),
      },
    },
  }, async () => {
    return await listUsers()
  })
}
```

```typescript
// index.ts
import { userRoutes } from './routes/users'

app.register(userRoutes, { prefix: '/api/v1' })
```

## Error Handling

```typescript
import { Type } from '@sinclair/typebox'

// Standard error schema
export const ErrorSchema = Type.Object({
  statusCode: Type.Number(),
  error: Type.String(),
  message: Type.String(),
})

// Custom error handler
app.setErrorHandler((error, request, reply) => {
  if (error.validation) {
    return reply.status(400).send({
      statusCode: 400,
      error: 'Validation Error',
      message: error.message,
    })
  }

  request.log.error(error)
  return reply.status(500).send({
    statusCode: 500,
    error: 'Internal Server Error',
    message: 'Something went wrong',
  })
})
```

## Reusable Schemas (Shared References)

```typescript
// Add schema to instance for $ref usage
app.addSchema({
  $id: 'User',
  ...UserSchema,
})

app.addSchema({
  $id: 'Error',
  ...ErrorSchema,
})

// Reference in routes
app.get('/me', {
  schema: {
    response: {
      200: Type.Ref('User'),
      401: Type.Ref('Error'),
    },
  },
}, handler)
```

## Headers and Auth

```typescript
const AuthHeadersSchema = Type.Object({
  authorization: Type.String({ pattern: '^Bearer .+$' }),
})

app.get('/protected', {
  schema: {
    headers: AuthHeadersSchema,
    response: { 200: UserSchema },
  },
  preValidation: async (request, reply) => {
    const token = request.headers.authorization?.replace('Bearer ', '')
    if (!token || !verifyToken(token)) {
      return reply.status(401).send({ message: 'Unauthorized' })
    }
  },
}, handler)
```

## Guidelines

1. Always define schemas with `Type.Object({ ... })` - full JSON Schema required in Fastify v5
2. Use `Static<typeof Schema>` to derive TypeScript types from schemas
3. Split input schemas (CreateX) from output schemas (X) - omit generated fields
4. Define response schemas for all status codes you return
5. Use `Type.Optional()` for optional fields, not `?` in the type
6. Export `FastifyTypebox` type for modular route files
7. Add format validators: `uuid`, `email`, `date-time`, `uri`
8. Use `Type.Union([Type.Literal(...)])` for string enums
