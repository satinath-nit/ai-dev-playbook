# Mock External Dependencies

> Generate mocks for APIs, databases, and external services to isolate unit tests.

## When to Use

- Testing code that calls external APIs
- Testing database operations without a real database
- Testing code with third-party service dependencies
- When you need fast, reliable tests that don't depend on external systems

## The Prompt

```text
I need to create mocks for external dependencies in my tests.

**Code to test**:
```[LANGUAGE]
[CODE]
```

**Dependencies to mock**:
- [DEPENDENCY_1]: [DESCRIPTION]
- [DEPENDENCY_2]: [DESCRIPTION]

**Testing framework**: [TEST_FRAMEWORK]

Please:
1. Identify all external dependencies that need mocking
2. Create mock implementations for each
3. Show how to inject the mocks in tests
4. Include examples of testing both success and error scenarios
```

## Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `[LANGUAGE]` | Programming language | `TypeScript`, `Python` |
| `[CODE]` | Code with dependencies | Your service or function |
| `[DEPENDENCY_1]` | External dependency | "HTTP client for user API" |
| `[TEST_FRAMEWORK]` | Testing framework | `Jest`, `pytest` |

## Example

### Input

```text
I need to create mocks for external dependencies in my tests.

**Code to test**:
```typescript
import { httpClient } from './httpClient';
import { cache } from './cache';

export async function getUserWithCache(userId: string): Promise<User> {
  const cached = await cache.get(`user:${userId}`);
  if (cached) return cached;
  
  const user = await httpClient.get(`/api/users/${userId}`);
  await cache.set(`user:${userId}`, user, { ttl: 3600 });
  return user;
}
```

**Dependencies to mock**:
- httpClient: Makes HTTP requests to user API
- cache: Redis cache for storing user data

**Testing framework**: Jest with TypeScript
```

### Expected Output

```typescript
import { getUserWithCache } from './userService';
import { httpClient } from './httpClient';
import { cache } from './cache';

// Mock the modules
jest.mock('./httpClient');
jest.mock('./cache');

const mockHttpClient = httpClient as jest.Mocked<typeof httpClient>;
const mockCache = cache as jest.Mocked<typeof cache>;

describe('getUserWithCache', () => {
  const mockUser = { id: '123', name: 'John', email: 'john@example.com' };

  beforeEach(() => {
    jest.clearAllMocks();
  });

  describe('cache hit', () => {
    it('should return cached user without calling API', async () => {
      mockCache.get.mockResolvedValue(mockUser);

      const result = await getUserWithCache('123');

      expect(result).toEqual(mockUser);
      expect(mockCache.get).toHaveBeenCalledWith('user:123');
      expect(mockHttpClient.get).not.toHaveBeenCalled();
    });
  });

  describe('cache miss', () => {
    it('should fetch from API and cache the result', async () => {
      mockCache.get.mockResolvedValue(null);
      mockHttpClient.get.mockResolvedValue(mockUser);
      mockCache.set.mockResolvedValue(undefined);

      const result = await getUserWithCache('123');

      expect(result).toEqual(mockUser);
      expect(mockHttpClient.get).toHaveBeenCalledWith('/api/users/123');
      expect(mockCache.set).toHaveBeenCalledWith('user:123', mockUser, { ttl: 3600 });
    });
  });

  describe('error handling', () => {
    it('should propagate API errors', async () => {
      mockCache.get.mockResolvedValue(null);
      mockHttpClient.get.mockRejectedValue(new Error('API unavailable'));

      await expect(getUserWithCache('123')).rejects.toThrow('API unavailable');
    });
  });
});
```

## Tips & Variations

### Tool-Specific Notes

- **Jest**: Use `jest.mock()` for module mocking
- **pytest**: Use `unittest.mock` or `pytest-mock`
- **Go**: Use interfaces and dependency injection

### Follow-up Prompts

```text
How do I verify the mock was called with specific arguments?
```

```text
Show me how to mock this dependency to return different values on successive calls.
```

## Related Playbooks

- [Generate Unit Tests](generate-unit-tests.md)
- [Test Edge Cases](test-edge-cases.md)
- [Improve Testability](../refactoring/improve-testability.md)
