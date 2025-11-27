# Generate Unit Tests

> Create comprehensive unit tests for existing code with proper coverage of edge cases and error handling.

## When to Use

- You have existing code without tests
- You need to add test coverage to legacy code
- You want to ensure your code handles edge cases
- You're preparing code for refactoring and need a safety net

## The Prompt

```text
I need you to generate comprehensive unit tests for the following code.

**Language/Framework**: [LANGUAGE] with [TEST_FRAMEWORK]

**Code to test**:
```[LANGUAGE]
[CODE]
```

**Requirements**:
- Test all public functions/methods
- Include edge cases and boundary conditions
- Test error handling and invalid inputs
- Use descriptive test names that explain what's being tested
- Follow the Arrange-Act-Assert pattern
- Mock external dependencies if needed

**Additional context** (optional):
[CONTEXT]

Please generate the tests and explain your reasoning for the test cases you chose.
```

## Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `[LANGUAGE]` | Programming language | `TypeScript`, `Python`, `Go` |
| `[TEST_FRAMEWORK]` | Testing framework to use | `Jest`, `pytest`, `go test` |
| `[CODE]` | The code you want to test | Your function or class |
| `[CONTEXT]` | Additional context about the code | "This is part of a payment processing system" |

## Example

### Input

```text
I need you to generate comprehensive unit tests for the following code.

**Language/Framework**: TypeScript with Jest

**Code to test**:
```typescript
interface User {
  id: string;
  email: string;
  age: number;
}

export function validateUser(user: Partial<User>): { valid: boolean; errors: string[] } {
  const errors: string[] = [];

  if (!user.id || user.id.trim() === '') {
    errors.push('ID is required');
  }

  if (!user.email || !user.email.includes('@')) {
    errors.push('Valid email is required');
  }

  if (user.age !== undefined && (user.age < 0 || user.age > 150)) {
    errors.push('Age must be between 0 and 150');
  }

  return {
    valid: errors.length === 0,
    errors
  };
}
```

**Requirements**:
- Test all public functions/methods
- Include edge cases and boundary conditions
- Test error handling and invalid inputs
- Use descriptive test names that explain what's being tested
- Follow the Arrange-Act-Assert pattern
- Mock external dependencies if needed

**Additional context**:
This is used in a user registration flow.
```

### Expected Output

```typescript
import { validateUser } from './validateUser';

describe('validateUser', () => {
  describe('valid users', () => {
    it('should return valid for a complete user with all fields', () => {
      const user = { id: '123', email: 'test@example.com', age: 25 };
      
      const result = validateUser(user);
      
      expect(result.valid).toBe(true);
      expect(result.errors).toHaveLength(0);
    });

    it('should return valid when age is not provided', () => {
      const user = { id: '123', email: 'test@example.com' };
      
      const result = validateUser(user);
      
      expect(result.valid).toBe(true);
      expect(result.errors).toHaveLength(0);
    });
  });

  describe('ID validation', () => {
    it('should return error when ID is missing', () => {
      const user = { email: 'test@example.com' };
      
      const result = validateUser(user);
      
      expect(result.valid).toBe(false);
      expect(result.errors).toContain('ID is required');
    });

    it('should return error when ID is empty string', () => {
      const user = { id: '', email: 'test@example.com' };
      
      const result = validateUser(user);
      
      expect(result.valid).toBe(false);
      expect(result.errors).toContain('ID is required');
    });

    it('should return error when ID is only whitespace', () => {
      const user = { id: '   ', email: 'test@example.com' };
      
      const result = validateUser(user);
      
      expect(result.valid).toBe(false);
      expect(result.errors).toContain('ID is required');
    });
  });

  describe('email validation', () => {
    it('should return error when email is missing', () => {
      const user = { id: '123' };
      
      const result = validateUser(user);
      
      expect(result.valid).toBe(false);
      expect(result.errors).toContain('Valid email is required');
    });

    it('should return error when email has no @ symbol', () => {
      const user = { id: '123', email: 'invalid-email' };
      
      const result = validateUser(user);
      
      expect(result.valid).toBe(false);
      expect(result.errors).toContain('Valid email is required');
    });
  });

  describe('age validation', () => {
    it('should return error when age is negative', () => {
      const user = { id: '123', email: 'test@example.com', age: -1 };
      
      const result = validateUser(user);
      
      expect(result.valid).toBe(false);
      expect(result.errors).toContain('Age must be between 0 and 150');
    });

    it('should return error when age exceeds 150', () => {
      const user = { id: '123', email: 'test@example.com', age: 151 };
      
      const result = validateUser(user);
      
      expect(result.valid).toBe(false);
      expect(result.errors).toContain('Age must be between 0 and 150');
    });

    it('should accept age at boundary value 0', () => {
      const user = { id: '123', email: 'test@example.com', age: 0 };
      
      const result = validateUser(user);
      
      expect(result.valid).toBe(true);
    });

    it('should accept age at boundary value 150', () => {
      const user = { id: '123', email: 'test@example.com', age: 150 };
      
      const result = validateUser(user);
      
      expect(result.valid).toBe(true);
    });
  });

  describe('multiple errors', () => {
    it('should return all errors when multiple validations fail', () => {
      const user = { age: -5 };
      
      const result = validateUser(user);
      
      expect(result.valid).toBe(false);
      expect(result.errors).toHaveLength(3);
      expect(result.errors).toContain('ID is required');
      expect(result.errors).toContain('Valid email is required');
      expect(result.errors).toContain('Age must be between 0 and 150');
    });
  });
});
```

## Tips & Variations

### Tool-Specific Notes

- **GitHub Copilot**: Works well in Copilot Chat. You can also highlight code and use `/tests` command
- **Cursor**: Paste into chat or use Cmd+K on selected code with "write tests"
- **Claude/ChatGPT**: Paste the full prompt for best results

### Common Pitfalls

- **AI generates tests that don't compile**: Always review imports and type definitions
- **Missing edge cases**: Explicitly ask for boundary conditions if not included
- **Over-mocking**: If tests mock too much, ask for more integration-style tests

### Follow-up Prompts

If tests are missing coverage:
```text
Please add tests for these additional scenarios:
- What happens when [SPECIFIC_SCENARIO]?
- Add tests for the boundary conditions of [FIELD]
- Add negative tests for [ERROR_CASE]
```

If tests are too verbose:
```text
Please refactor these tests to use test.each/parameterized tests for the similar cases.
```

### Variations

**Quick version** for simple functions:
```text
Write Jest tests for this function, covering happy path and edge cases:
[CODE]
```

**Detailed version** for complex classes:
```text
Generate a complete test suite for this class including:
- Unit tests for each public method
- Integration tests for method interactions
- Tests for state management
- Error handling tests
- Performance considerations
```

## Related Playbooks

- [Test Edge Cases](test-edge-cases.md)
- [Mock External Dependencies](mock-dependencies.md)
- [Improve Testability](../refactoring/improve-testability.md)
