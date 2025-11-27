# Extract Module

> Break down large files into smaller, focused modules while maintaining functionality and improving maintainability.

## When to Use

- A file has grown too large (300+ lines)
- A file has multiple unrelated responsibilities
- You want to improve code organization
- You need to make code more testable
- You're preparing for team scaling

## The Prompt

```text
I need to refactor this large file by extracting it into smaller, focused modules.

**Current Code**:
```[LANGUAGE]
[CODE]
```

**Goals**:
- Each module should have a single responsibility
- Maintain all existing functionality
- Preserve the public API (or document breaking changes)
- Improve testability
- Follow [LANGUAGE] best practices for module organization

**Constraints** (if any):
[CONSTRAINTS]

Please:
1. Analyze the code and identify logical groupings
2. Propose a module structure with file names
3. Show the refactored code for each new module
4. Show how to update imports in consuming code
5. Highlight any potential issues or breaking changes
```

## Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `[LANGUAGE]` | Programming language | `TypeScript`, `Python`, `Go` |
| `[CODE]` | The large file to refactor | Your 500-line file |
| `[CONSTRAINTS]` | Any limitations | "Can't change the public API", "Must work with existing tests" |

## Example

### Input

```text
I need to refactor this large file by extracting it into smaller, focused modules.

**Current Code**:
```typescript
// userService.ts - 400 lines, doing too much

import { db } from './database';
import { sendEmail } from './email';
import { hash, compare } from 'bcrypt';
import { sign, verify } from 'jsonwebtoken';

// Types
interface User {
  id: string;
  email: string;
  passwordHash: string;
  role: 'admin' | 'user';
  createdAt: Date;
}

interface CreateUserInput {
  email: string;
  password: string;
  role?: 'admin' | 'user';
}

// Validation functions
function validateEmail(email: string): boolean {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
}

function validatePassword(password: string): { valid: boolean; errors: string[] } {
  const errors: string[] = [];
  if (password.length < 8) errors.push('Password must be at least 8 characters');
  if (!/[A-Z]/.test(password)) errors.push('Password must contain uppercase');
  if (!/[0-9]/.test(password)) errors.push('Password must contain a number');
  return { valid: errors.length === 0, errors };
}

// Auth functions
async function hashPassword(password: string): Promise<string> {
  return hash(password, 10);
}

async function verifyPassword(password: string, hash: string): Promise<boolean> {
  return compare(password, hash);
}

function generateToken(user: User): string {
  return sign({ userId: user.id, role: user.role }, process.env.JWT_SECRET!, { expiresIn: '24h' });
}

function verifyToken(token: string): { userId: string; role: string } | null {
  try {
    return verify(token, process.env.JWT_SECRET!) as { userId: string; role: string };
  } catch {
    return null;
  }
}

// User CRUD
async function createUser(input: CreateUserInput): Promise<User> {
  if (!validateEmail(input.email)) {
    throw new Error('Invalid email');
  }
  const passwordValidation = validatePassword(input.password);
  if (!passwordValidation.valid) {
    throw new Error(passwordValidation.errors.join(', '));
  }
  
  const passwordHash = await hashPassword(input.password);
  const user = await db.users.create({
    email: input.email,
    passwordHash,
    role: input.role || 'user',
    createdAt: new Date()
  });
  
  await sendEmail(input.email, 'Welcome!', 'Thanks for signing up');
  return user;
}

async function getUserById(id: string): Promise<User | null> {
  return db.users.findById(id);
}

async function updateUser(id: string, updates: Partial<User>): Promise<User> {
  return db.users.update(id, updates);
}

async function deleteUser(id: string): Promise<void> {
  await db.users.delete(id);
}

// Login/logout
async function login(email: string, password: string): Promise<{ user: User; token: string }> {
  const user = await db.users.findByEmail(email);
  if (!user) throw new Error('User not found');
  
  const valid = await verifyPassword(password, user.passwordHash);
  if (!valid) throw new Error('Invalid password');
  
  const token = generateToken(user);
  return { user, token };
}

export {
  User, CreateUserInput,
  validateEmail, validatePassword,
  createUser, getUserById, updateUser, deleteUser,
  login, generateToken, verifyToken
};
```

**Goals**:
- Each module should have a single responsibility
- Maintain all existing functionality
- Preserve the public API
- Improve testability

**Constraints**:
- Keep the same export names for backward compatibility
```

### Expected Output

**Proposed Module Structure**:

```
src/
├── user/
│   ├── index.ts           # Re-exports public API
│   ├── types.ts           # User types and interfaces
│   ├── validation.ts      # Email and password validation
│   ├── auth.ts            # Password hashing, JWT functions
│   ├── repository.ts      # Database operations
│   └── service.ts         # Business logic (createUser, login)
```

**types.ts**:
```typescript
export interface User {
  id: string;
  email: string;
  passwordHash: string;
  role: 'admin' | 'user';
  createdAt: Date;
}

export interface CreateUserInput {
  email: string;
  password: string;
  role?: 'admin' | 'user';
}
```

**validation.ts**:
```typescript
export function validateEmail(email: string): boolean {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
}

export function validatePassword(password: string): { valid: boolean; errors: string[] } {
  const errors: string[] = [];
  if (password.length < 8) errors.push('Password must be at least 8 characters');
  if (!/[A-Z]/.test(password)) errors.push('Password must contain uppercase');
  if (!/[0-9]/.test(password)) errors.push('Password must contain a number');
  return { valid: errors.length === 0, errors };
}
```

**auth.ts**:
```typescript
import { hash, compare } from 'bcrypt';
import { sign, verify } from 'jsonwebtoken';
import { User } from './types';

export async function hashPassword(password: string): Promise<string> {
  return hash(password, 10);
}

export async function verifyPassword(password: string, hash: string): Promise<boolean> {
  return compare(password, hash);
}

export function generateToken(user: User): string {
  return sign(
    { userId: user.id, role: user.role },
    process.env.JWT_SECRET!,
    { expiresIn: '24h' }
  );
}

export function verifyToken(token: string): { userId: string; role: string } | null {
  try {
    return verify(token, process.env.JWT_SECRET!) as { userId: string; role: string };
  } catch {
    return null;
  }
}
```

**repository.ts**:
```typescript
import { db } from '../database';
import { User } from './types';

export async function findById(id: string): Promise<User | null> {
  return db.users.findById(id);
}

export async function findByEmail(email: string): Promise<User | null> {
  return db.users.findByEmail(email);
}

export async function create(data: Omit<User, 'id'>): Promise<User> {
  return db.users.create(data);
}

export async function update(id: string, updates: Partial<User>): Promise<User> {
  return db.users.update(id, updates);
}

export async function remove(id: string): Promise<void> {
  await db.users.delete(id);
}
```

**service.ts**:
```typescript
import { sendEmail } from '../email';
import { User, CreateUserInput } from './types';
import { validateEmail, validatePassword } from './validation';
import { hashPassword, verifyPassword, generateToken } from './auth';
import * as userRepo from './repository';

export async function createUser(input: CreateUserInput): Promise<User> {
  if (!validateEmail(input.email)) {
    throw new Error('Invalid email');
  }
  
  const passwordValidation = validatePassword(input.password);
  if (!passwordValidation.valid) {
    throw new Error(passwordValidation.errors.join(', '));
  }
  
  const passwordHash = await hashPassword(input.password);
  const user = await userRepo.create({
    email: input.email,
    passwordHash,
    role: input.role || 'user',
    createdAt: new Date()
  });
  
  await sendEmail(input.email, 'Welcome!', 'Thanks for signing up');
  return user;
}

export async function getUserById(id: string): Promise<User | null> {
  return userRepo.findById(id);
}

export async function updateUser(id: string, updates: Partial<User>): Promise<User> {
  return userRepo.update(id, updates);
}

export async function deleteUser(id: string): Promise<void> {
  await userRepo.remove(id);
}

export async function login(email: string, password: string): Promise<{ user: User; token: string }> {
  const user = await userRepo.findByEmail(email);
  if (!user) throw new Error('User not found');
  
  const valid = await verifyPassword(password, user.passwordHash);
  if (!valid) throw new Error('Invalid password');
  
  const token = generateToken(user);
  return { user, token };
}
```

**index.ts** (preserves public API):
```typescript
export { User, CreateUserInput } from './types';
export { validateEmail, validatePassword } from './validation';
export { generateToken, verifyToken } from './auth';
export {
  createUser,
  getUserById,
  updateUser,
  deleteUser,
  login
} from './service';
```

## Tips & Variations

### Tool-Specific Notes

- **GitHub Copilot**: Break into multiple prompts if the file is very large
- **Cursor**: Use Composer mode for multi-file refactoring
- **Claude**: Can handle larger files in a single prompt

### Common Pitfalls

- **Circular dependencies**: Watch for modules importing each other
- **Over-extraction**: Don't create too many tiny files
- **Breaking changes**: Ensure the public API remains compatible

### Follow-up Prompts

```text
Can you also generate tests for the new [MODULE_NAME] module?
```

```text
I'm getting a circular dependency between [MODULE_A] and [MODULE_B]. How should I restructure?
```

## Related Playbooks

- [Break Up God Class](break-up-god-class.md)
- [Improve Testability](improve-testability.md)
- [Remove Code Duplication](remove-duplication.md)
