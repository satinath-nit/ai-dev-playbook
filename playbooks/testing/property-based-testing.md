# Property-Based Testing

> Create property-based tests that automatically generate test cases to find edge cases you might miss.

## When to Use

- Testing pure functions with well-defined properties
- When you want to find edge cases automatically
- Testing serialization/deserialization roundtrips
- Testing mathematical or algorithmic code
- When example-based tests feel incomplete

## The Prompt

```text
Help me create property-based tests for this code.

**Code to test**:
```[LANGUAGE]
[CODE]
```

**What properties should hold true**:
- [PROPERTY_1]
- [PROPERTY_2]

**Testing framework**: [FRAMEWORK]

Please:
1. Identify properties that should always hold for this code
2. Create property-based tests using appropriate generators
3. Show how to shrink failing cases to minimal examples
4. Include any invariants that should be maintained
```

## Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `[LANGUAGE]` | Programming language | `TypeScript`, `Python` |
| `[CODE]` | Code to test | Your function |
| `[PROPERTY_1]` | A property that should hold | "Sorting should be idempotent" |
| `[FRAMEWORK]` | PBT framework | `fast-check`, `hypothesis` |

## Example

### Input

```text
Help me create property-based tests for this code.

**Code to test**:
```typescript
function sortByAge(users: User[]): User[] {
  return [...users].sort((a, b) => a.age - b.age);
}

interface User {
  id: string;
  name: string;
  age: number;
}
```

**Testing framework**: fast-check with Jest
```

### Expected Output

```typescript
import fc from 'fast-check';
import { sortByAge, User } from './users';

// Generator for User objects
const userArbitrary = fc.record({
  id: fc.uuid(),
  name: fc.string({ minLength: 1, maxLength: 50 }),
  age: fc.integer({ min: 0, max: 150 })
});

describe('sortByAge properties', () => {
  it('should be idempotent (sorting twice equals sorting once)', () => {
    fc.assert(
      fc.property(fc.array(userArbitrary), (users) => {
        const sortedOnce = sortByAge(users);
        const sortedTwice = sortByAge(sortedOnce);
        expect(sortedTwice).toEqual(sortedOnce);
      })
    );
  });

  it('should preserve all elements (same length, same elements)', () => {
    fc.assert(
      fc.property(fc.array(userArbitrary), (users) => {
        const sorted = sortByAge(users);
        expect(sorted.length).toBe(users.length);
        
        const originalIds = users.map(u => u.id).sort();
        const sortedIds = sorted.map(u => u.id).sort();
        expect(sortedIds).toEqual(originalIds);
      })
    );
  });

  it('should produce ordered output (each age <= next age)', () => {
    fc.assert(
      fc.property(fc.array(userArbitrary), (users) => {
        const sorted = sortByAge(users);
        for (let i = 0; i < sorted.length - 1; i++) {
          expect(sorted[i].age).toBeLessThanOrEqual(sorted[i + 1].age);
        }
      })
    );
  });

  it('should not mutate the original array', () => {
    fc.assert(
      fc.property(fc.array(userArbitrary), (users) => {
        const original = [...users];
        sortByAge(users);
        expect(users).toEqual(original);
      })
    );
  });
});
```

## Common Properties to Test

| Property | Description | Example |
|----------|-------------|---------|
| Idempotence | f(f(x)) = f(x) | Sorting, normalization |
| Roundtrip | decode(encode(x)) = x | Serialization |
| Commutativity | f(a, b) = f(b, a) | Addition, set union |
| Associativity | f(f(a, b), c) = f(a, f(b, c)) | String concat |
| Identity | f(x, identity) = x | Adding zero |
| Invariant | Some property always holds | Length preserved |

## Tips & Variations

### Follow-up Prompts

```text
The test found a failing case. Help me understand why [INPUT] causes the failure.
```

```text
What other properties should I test for this [TYPE] of function?
```

## Related Playbooks

- [Generate Unit Tests](generate-unit-tests.md)
- [Test Edge Cases](test-edge-cases.md)
