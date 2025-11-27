# Test Edge Cases

> Identify and test edge cases, boundary conditions, and corner cases that are often missed.

## When to Use

- You have a function but aren't sure what edge cases to test
- You want to improve test coverage
- You're testing code that handles user input
- You need to ensure robustness before production

## The Prompt

```text
I need help identifying and testing edge cases for this code.

**Code to analyze**:
```[LANGUAGE]
[CODE]
```

**What this code does**: [DESCRIPTION]

Please:
1. List all edge cases and boundary conditions I should test
2. Categorize them (input validation, boundary values, error conditions, etc.)
3. For each edge case, explain WHY it's important to test
4. Generate test code for the top 10 most critical edge cases

Focus on cases that could cause:
- Crashes or exceptions
- Incorrect results
- Security vulnerabilities
- Performance issues
```

## Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `[LANGUAGE]` | Programming language | `TypeScript`, `Python` |
| `[CODE]` | The code to analyze | Your function or class |
| `[DESCRIPTION]` | What the code does | "Parses user-provided dates" |

## Example

### Input

```text
I need help identifying and testing edge cases for this code.

**Code to analyze**:
```typescript
function paginate<T>(items: T[], page: number, pageSize: number): {
  data: T[];
  totalPages: number;
  currentPage: number;
  hasNext: boolean;
  hasPrev: boolean;
} {
  const totalPages = Math.ceil(items.length / pageSize);
  const start = (page - 1) * pageSize;
  const end = start + pageSize;
  
  return {
    data: items.slice(start, end),
    totalPages,
    currentPage: page,
    hasNext: page < totalPages,
    hasPrev: page > 1
  };
}
```

**What this code does**: Paginates an array of items for display in a UI.
```

### Expected Output

**Edge Cases Identified**:

**1. Empty Input**
| Case | Input | Why It Matters |
|------|-------|----------------|
| Empty array | `items = []` | Common case, should not crash |
| Null/undefined items | `items = null` | Defensive programming |

**2. Boundary Values - Page Number**
| Case | Input | Why It Matters |
|------|-------|----------------|
| Page 0 | `page = 0` | Invalid, should handle gracefully |
| Negative page | `page = -1` | Invalid input |
| Page beyond total | `page = 100` when only 3 pages | Common user error |
| First page | `page = 1` | Boundary condition |
| Last page | `page = totalPages` | Boundary condition |

**3. Boundary Values - Page Size**
| Case | Input | Why It Matters |
|------|-------|----------------|
| Page size 0 | `pageSize = 0` | Division by zero risk |
| Negative page size | `pageSize = -1` | Invalid input |
| Page size = 1 | `pageSize = 1` | Minimum valid size |
| Page size > items | `pageSize = 100`, 5 items | Single page scenario |

**4. Type Edge Cases**
| Case | Input | Why It Matters |
|------|-------|----------------|
| Floating point page | `page = 1.5` | Should be integer |
| Very large numbers | `page = Number.MAX_SAFE_INTEGER` | Overflow risk |

**Test Code**:

```typescript
describe('paginate', () => {
  describe('empty and null inputs', () => {
    it('should return empty data for empty array', () => {
      const result = paginate([], 1, 10);
      expect(result.data).toEqual([]);
      expect(result.totalPages).toBe(0);
      expect(result.hasNext).toBe(false);
      expect(result.hasPrev).toBe(false);
    });
  });

  describe('page number boundaries', () => {
    const items = [1, 2, 3, 4, 5];

    it('should handle page 0 gracefully', () => {
      const result = paginate(items, 0, 2);
      // Define expected behavior - return first page or empty?
      expect(result.data).toEqual([]);
    });

    it('should handle negative page number', () => {
      const result = paginate(items, -1, 2);
      expect(result.data).toEqual([]);
    });

    it('should return empty for page beyond total', () => {
      const result = paginate(items, 100, 2);
      expect(result.data).toEqual([]);
      expect(result.hasNext).toBe(false);
    });

    it('should correctly identify first page', () => {
      const result = paginate(items, 1, 2);
      expect(result.hasPrev).toBe(false);
      expect(result.hasNext).toBe(true);
    });

    it('should correctly identify last page', () => {
      const result = paginate(items, 3, 2);
      expect(result.hasPrev).toBe(true);
      expect(result.hasNext).toBe(false);
    });
  });

  describe('page size boundaries', () => {
    const items = [1, 2, 3, 4, 5];

    it('should handle page size of 0', () => {
      // This will cause division by zero - should throw or handle
      expect(() => paginate(items, 1, 0)).toThrow();
    });

    it('should handle page size larger than items', () => {
      const result = paginate(items, 1, 100);
      expect(result.data).toEqual(items);
      expect(result.totalPages).toBe(1);
      expect(result.hasNext).toBe(false);
    });

    it('should handle page size of 1', () => {
      const result = paginate(items, 3, 1);
      expect(result.data).toEqual([3]);
      expect(result.totalPages).toBe(5);
    });
  });

  describe('exact boundary conditions', () => {
    it('should handle items exactly divisible by page size', () => {
      const items = [1, 2, 3, 4];
      const result = paginate(items, 2, 2);
      expect(result.data).toEqual([3, 4]);
      expect(result.totalPages).toBe(2);
    });
  });
});
```

## Tips & Variations

### Common Edge Case Categories

1. **Null/Undefined/Empty**: Always test missing or empty inputs
2. **Boundaries**: Min, max, zero, negative, off-by-one
3. **Type coercion**: Strings where numbers expected, floats where integers expected
4. **Size extremes**: Very large inputs, single element, maximum values
5. **Special characters**: Unicode, emojis, control characters in strings
6. **Concurrency**: Race conditions, simultaneous access
7. **State**: Uninitialized, partially initialized, already processed

### Follow-up Prompts

```text
Which of these edge cases are most likely to cause production issues?
```

```text
Generate property-based tests that would catch these edge cases automatically.
```

## Related Playbooks

- [Generate Unit Tests](generate-unit-tests.md)
- [Property-Based Testing](property-based-testing.md)
