# Next.js Project Architecture

## Layered Architecture Overview

```
┌─────────────────────────────────────────┐
│     Presentation Layer (UI)             │
│  - React Components                     │
│  - Pages & Layouts                      │
│  - Client-side interactions             │
└─────────────────────────────────────────┘
                  ↓
┌─────────────────────────────────────────┐
│     Application Layer (Business Logic)  │
│  - Server Actions                       │
│  - API Route Handlers                   │
│  - Use Cases / Services                 │
└─────────────────────────────────────────┘
                  ↓
┌─────────────────────────────────────────┐
│     Domain Layer (Business Rules)       │
│  - Business logic                       │
│  - Validation                           │
│  - Domain models                        │
└─────────────────────────────────────────┘
                  ↓
┌─────────────────────────────────────────┐
│     Data Access Layer (Persistence)     │
│  - Database queries                     │
│  - External APIs                        │
│  - Data repositories                    │
└─────────────────────────────────────────┘
```

## Recommended Folder Structure

```
nextjs-app/
├── app/                          # Next.js App Router (Presentation Layer)
│   ├── (auth)/                   # Route group for authentication
│   │   ├── login/
│   │   │   └── page.tsx          # Login page
│   │   └── register/
│   │       └── page.tsx          # Register page
│   │
│   ├── (dashboard)/              # Route group for dashboard
│   │   ├── layout.tsx            # Dashboard layout
│   │   ├── page.tsx              # Dashboard home
│   │   ├── users/
│   │   │   ├── page.tsx          # Users list
│   │   │   ├── [id]/
│   │   │   │   └── page.tsx      # User detail
│   │   │   └── create/
│   │   │       └── page.tsx      # Create user
│   │   └── settings/
│   │       └── page.tsx
│   │
│   ├── api/                      # API Routes (Application Layer)
│   │   ├── users/
│   │   │   └── route.ts          # GET, POST /api/users
│   │   ├── users/[id]/
│   │   │   └── route.ts          # GET, PUT, DELETE /api/users/:id
│   │   └── auth/
│   │       └── route.ts
│   │
│   ├── layout.tsx                # Root layout
│   ├── page.tsx                  # Home page
│   └── error.tsx                 # Error boundary
│
├── src/
│   ├── components/               # Presentation Layer
│   │   ├── ui/                   # Reusable UI components
│   │   │   ├── button.tsx
│   │   │   ├── input.tsx
│   │   │   ├── card.tsx
│   │   │   └── modal.tsx
│   │   ├── forms/                # Form components
│   │   │   ├── login-form.tsx
│   │   │   └── user-form.tsx
│   │   └── features/             # Feature-specific components
│   │       ├── user-list.tsx
│   │       ├── user-card.tsx
│   │       └── dashboard-stats.tsx
│   │
│   ├── actions/                  # Application Layer (Server Actions)
│   │   ├── user-actions.ts       # User-related server actions
│   │   ├── auth-actions.ts       # Authentication actions
│   │   └── invoice-actions.ts
│   │
│   ├── services/                 # Application Layer (Business Logic)
│   │   ├── user-service.ts       # User business logic
│   │   ├── auth-service.ts       # Auth business logic
│   │   ├── email-service.ts      # Email service
│   │   └── payment-service.ts
│   │
│   ├── lib/                      # Domain Layer & Utilities
│   │   ├── validations/          # Input validation schemas
│   │   │   ├── user-schema.ts
│   │   │   └── auth-schema.ts
│   │   ├── utils/                # Utility functions
│   │   │   ├── format.ts
│   │   │   ├── date.ts
│   │   │   └── string.ts
│   │   ├── constants/
│   │   │   └── app-constants.ts
│   │   └── types/                # TypeScript types
│   │       ├── user.ts
│   │       ├── invoice.ts
│   │       └── common.ts
│   │
│   ├── repositories/             # Data Access Layer
│   │   ├── user-repository.ts    # User database operations
│   │   ├── invoice-repository.ts
│   │   └── base-repository.ts
│   │
│   ├── models/                   # Domain Layer (Domain Models)
│   │   ├── user.ts               # User domain model
│   │   ├── invoice.ts
│   │   └── customer.ts
│   │
│   ├── database/                 # Data Access Layer
│   │   ├── db.ts                 # Database connection
│   │   ├── schema.sql            # Database schema
│   │   └── migrations/           # Database migrations
│   │       ├── 001_initial.sql
│   │       └── 002_add_users.sql
│   │
│   ├── middleware/               # Application Layer
│   │   ├── auth-middleware.ts
│   │   ├── logging-middleware.ts
│   │   └── rate-limit.ts
│   │
│   └── hooks/                    # Presentation Layer (React hooks)
│       ├── use-user.ts
│       ├── use-auth.ts
│       └── use-debounce.ts
│
├── public/                       # Static assets
│   ├── images/
│   ├── icons/
│   └── fonts/
│
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
│
├── .env.local                    # Environment variables
├── next.config.js
├── package.json
├── tsconfig.json
└── tailwind.config.ts
```

## Layer Descriptions

### 1. Presentation Layer
**Location:** `app/`, `src/components/`, `src/hooks/`

**Responsibility:**
- Display UI to users
- Handle user interactions
- Render data
- Client-side state management

**Example:**
```typescript
// src/components/features/user-list.tsx
'use client'

import { User } from '@/lib/types/user'
import { UserCard } from './user-card'

interface UserListProps {
  users: User[]
}

export function UserList({ users }: UserListProps) {
  return (
    <div className="grid gap-4">
      {users.map(user => (
        <UserCard key={user.id} user={user} />
      ))}
    </div>
  )
}
```

### 2. Application Layer
**Location:** `src/actions/`, `src/services/`, `app/api/`

**Responsibility:**
- Coordinate business operations
- Handle application workflows
- Process user requests
- Orchestrate between layers

**Example - Server Actions:**
```typescript
// src/actions/user-actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import { UserService } from '@/services/user-service'
import { userSchema } from '@/lib/validations/user-schema'

export async function createUser(formData: FormData) {
  // Validate input
  const validated = userSchema.parse({
    name: formData.get('name'),
    email: formData.get('email'),
  })

  // Call service layer
  const user = await UserService.create(validated)

  // Revalidate cache
  revalidatePath('/dashboard/users')

  return { success: true, user }
}
```

**Example - API Route:**
```typescript
// app/api/users/route.ts
import { NextRequest, NextResponse } from 'next/server'
import { UserService } from '@/services/user-service'

export async function GET(request: NextRequest) {
  const users = await UserService.getAll()
  return NextResponse.json(users)
}

export async function POST(request: NextRequest) {
  const body = await request.json()
  const user = await UserService.create(body)
  return NextResponse.json(user, { status: 201 })
}
```

### 3. Domain Layer
**Location:** `src/models/`, `src/lib/validations/`, `src/lib/types/`

**Responsibility:**
- Business rules and logic
- Domain models
- Validation rules
- Business invariants

**Example:**
```typescript
// src/models/user.ts
import { z } from 'zod'

export class User {
  constructor(
    public id: string,
    public name: string,
    public email: string,
    public role: 'admin' | 'user',
    public createdAt: Date
  ) {}

  // Business logic
  isAdmin(): boolean {
    return this.role === 'admin'
  }

  canEditUser(targetUser: User): boolean {
    return this.isAdmin() || this.id === targetUser.id
  }

  getDisplayName(): string {
    return this.name || this.email.split('@')[0]
  }
}

// src/lib/validations/user-schema.ts
export const userSchema = z.object({
  name: z.string().min(2).max(100),
  email: z.string().email(),
  role: z.enum(['admin', 'user']).default('user'),
})
```

### 4. Data Access Layer
**Location:** `src/repositories/`, `src/database/`

**Responsibility:**
- Database queries
- External API calls
- Data persistence
- Data retrieval

**Example:**
```typescript
// src/repositories/user-repository.ts
import { sql } from '@/database/db'
import { User } from '@/models/user'

export class UserRepository {
  static async findById(id: string): Promise<User | null> {
    const result = await sql`
      SELECT * FROM users WHERE id = ${id}
    `

    if (result.length === 0) return null

    return new User(
      result[0].id,
      result[0].name,
      result[0].email,
      result[0].role,
      result[0].created_at
    )
  }

  static async create(data: {
    name: string
    email: string
    role: string
  }): Promise<User> {
    const result = await sql`
      INSERT INTO users (name, email, role)
      VALUES (${data.name}, ${data.email}, ${data.role})
      RETURNING *
    `

    return new User(
      result[0].id,
      result[0].name,
      result[0].email,
      result[0].role,
      result[0].created_at
    )
  }

  static async findAll(): Promise<User[]> {
    const results = await sql`SELECT * FROM users ORDER BY created_at DESC`

    return results.map(row => new User(
      row.id,
      row.name,
      row.email,
      row.role,
      row.created_at
    ))
  }
}
```

### 5. Service Layer (Business Logic)
**Location:** `src/services/`

**Responsibility:**
- Complex business operations
- Transaction management
- Multiple repository coordination
- Business workflow orchestration

**Example:**
```typescript
// src/services/user-service.ts
import { UserRepository } from '@/repositories/user-repository'
import { EmailService } from './email-service'
import { userSchema } from '@/lib/validations/user-schema'

export class UserService {
  static async create(data: unknown) {
    // Validate
    const validated = userSchema.parse(data)

    // Check if email exists
    const existing = await UserRepository.findByEmail(validated.email)
    if (existing) {
      throw new Error('Email already exists')
    }

    // Create user
    const user = await UserRepository.create(validated)

    // Send welcome email (side effect)
    await EmailService.sendWelcome(user.email, user.name)

    return user
  }

  static async getAll() {
    return UserRepository.findAll()
  }

  static async updateRole(userId: string, newRole: 'admin' | 'user') {
    const user = await UserRepository.findById(userId)
    if (!user) {
      throw new Error('User not found')
    }

    // Business rule: can't remove last admin
    if (user.role === 'admin' && newRole === 'user') {
      const adminCount = await UserRepository.countAdmins()
      if (adminCount <= 1) {
        throw new Error('Cannot remove the last admin')
      }
    }

    return UserRepository.updateRole(userId, newRole)
  }
}
```

## Data Flow Example

Let's trace a request to create a user:

```
1. User fills form in UI
   ↓ (app/dashboard/users/create/page.tsx)

2. Form submits to Server Action
   ↓ (src/actions/user-actions.ts)

3. Server Action validates input
   ↓ (src/lib/validations/user-schema.ts)

4. Calls Service Layer
   ↓ (src/services/user-service.ts)

5. Service checks business rules
   ↓ applies domain logic

6. Service calls Repository
   ↓ (src/repositories/user-repository.ts)

7. Repository executes SQL
   ↓ (src/database/db.ts)

8. Data saved to Database
   ↓ returns User model

9. Service sends welcome email
   ↓ (src/services/email-service.ts)

10. Returns to UI with success
    ↓ revalidates cache

11. UI updates with new user
```

## Best Practices

### 1. Dependency Direction
Dependencies should flow downward:
- Presentation → Application → Domain → Data Access
- Never import upward (e.g., Repository shouldn't import Service)

### 2. Separation of Concerns
- **Pages/Components**: Only render UI, no business logic
- **Actions/API Routes**: Coordinate, don't contain business rules
- **Services**: Business logic, orchestration
- **Repositories**: Only database operations
- **Models**: Domain entities and rules

### 3. Keep Layers Independent
```typescript
// ❌ Bad: Component directly accessing repository
import { UserRepository } from '@/repositories/user-repository'

export function UserList() {
  const users = await UserRepository.findAll() // NO!
}

// ✅ Good: Component uses Server Action
import { getUsers } from '@/actions/user-actions'

export async function UserList() {
  const users = await getUsers() // YES!
}
```

### 4. Use TypeScript Properly
```typescript
// Define types in domain layer
// src/lib/types/user.ts
export interface User {
  id: string
  name: string
  email: string
  role: 'admin' | 'user'
}

// Use across all layers
import type { User } from '@/lib/types/user'
```

### 5. Error Handling Per Layer
```typescript
// Repository: Throw data access errors
throw new DatabaseError('Failed to connect')

// Service: Throw business logic errors
throw new BusinessRuleError('Cannot delete last admin')

// Action/API: Catch and return user-friendly errors
try {
  await UserService.delete(id)
} catch (error) {
  return { error: 'Unable to delete user' }
}

// Component: Display errors to user
{error && <ErrorMessage>{error}</ErrorMessage>}
```

## Conclusion

This architecture provides:
- **Clear separation of concerns**
- **Testability** - Each layer can be tested independently
- **Maintainability** - Changes in one layer don't affect others
- **Scalability** - Easy to add features without breaking existing code
- **Type safety** - TypeScript types flow through all layers
