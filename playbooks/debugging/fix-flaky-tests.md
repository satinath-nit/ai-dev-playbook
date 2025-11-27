# Fix Flaky Tests

> Diagnose and fix intermittent test failures that pass sometimes and fail other times.

## When to Use

- Tests pass locally but fail in CI (or vice versa)
- Tests fail randomly without code changes
- Tests fail when run in a different order
- Tests fail under load or parallel execution

## The Prompt

```text
I have a flaky test that passes sometimes and fails other times. Help me diagnose and fix it.

**Test Code**:
```[LANGUAGE]
[TEST_CODE]
```

**Code Being Tested** (if relevant):
```[LANGUAGE]
[SOURCE_CODE]
```

**Failure Pattern**:
- Fails approximately: [FREQUENCY] (e.g., "1 in 5 runs")
- Fails more often in: [ENVIRONMENT] (e.g., "CI", "parallel runs")
- Error message when it fails: [ERROR_MESSAGE]

**What I've tried**:
[ATTEMPTS]

Please:
1. Identify the most likely causes of flakiness
2. Explain why each cause leads to intermittent failures
3. Provide specific fixes for each cause
4. Suggest how to verify the fix worked
```

## Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `[LANGUAGE]` | Programming language | `TypeScript`, `Python` |
| `[TEST_CODE]` | The flaky test | Your test function |
| `[SOURCE_CODE]` | Code being tested | The function under test |
| `[FREQUENCY]` | How often it fails | "1 in 10 runs", "only in CI" |
| `[ENVIRONMENT]` | Where it fails most | "CI", "parallel execution" |
| `[ERROR_MESSAGE]` | The failure message | "Expected X but got Y" |
| `[ATTEMPTS]` | What you've tried | "Added retries, increased timeout" |

## Example

### Input

```text
I have a flaky test that passes sometimes and fails other times. Help me diagnose and fix it.

**Test Code**:
```typescript
describe('NotificationService', () => {
  it('should send notification after user signup', async () => {
    const user = await createUser({ email: 'test@example.com' });
    
    await signupUser(user);
    
    const notifications = await getNotifications(user.id);
    expect(notifications).toHaveLength(1);
    expect(notifications[0].type).toBe('welcome');
  });
});
```

**Code Being Tested**:
```typescript
async function signupUser(user: User) {
  await saveUser(user);
  
  // Send welcome notification asynchronously
  sendWelcomeNotification(user.id).catch(console.error);
  
  return user;
}
```

**Failure Pattern**:
- Fails approximately: 3 in 10 runs
- Fails more often in: CI (almost never fails locally)
- Error message: "Expected array of length 1, received array of length 0"

**What I've tried**:
- Added a 100ms delay before checking notifications
- Increased Jest timeout to 30 seconds
```

### Expected Output

**Root Cause Analysis**:

The test is flaky because of a **race condition**. The `signupUser` function fires `sendWelcomeNotification` asynchronously without awaiting it (fire-and-forget pattern). The test checks for notifications before the async operation completes.

**Why it's intermittent**:
- Locally: Your machine is fast, notification usually saves before the assertion
- In CI: Shared resources, slower I/O, notification often hasn't saved yet
- The 100ms delay helps but isn't reliable - timing-based fixes are inherently flaky

**Causes of Flakiness (in order of likelihood)**:

1. **Race Condition** (Most Likely)
   - Async operation not awaited
   - Test asserts before operation completes

2. **Shared State**
   - Database not cleaned between tests
   - Previous test's notifications appearing

3. **Time Dependency**
   - If notification has timestamp-based logic
   - Different timezones in CI

**Fixes**:

**Fix 1: Await the async operation (Recommended)**

```typescript
// Option A: Make signupUser await the notification
async function signupUser(user: User) {
  await saveUser(user);
  await sendWelcomeNotification(user.id);
  return user;
}

// Option B: Return a promise the caller can await
async function signupUser(user: User) {
  await saveUser(user);
  const notificationPromise = sendWelcomeNotification(user.id);
  return { user, notificationPromise };
}

// Test
it('should send notification after user signup', async () => {
  const user = await createUser({ email: 'test@example.com' });
  const { notificationPromise } = await signupUser(user);
  await notificationPromise;
  
  const notifications = await getNotifications(user.id);
  expect(notifications).toHaveLength(1);
});
```

**Fix 2: Use polling with timeout (If you can't change the source)**

```typescript
import { waitFor } from '@testing-library/react'; // or custom implementation

it('should send notification after user signup', async () => {
  const user = await createUser({ email: 'test@example.com' });
  await signupUser(user);
  
  await waitFor(async () => {
    const notifications = await getNotifications(user.id);
    expect(notifications).toHaveLength(1);
    expect(notifications[0].type).toBe('welcome');
  }, { timeout: 5000, interval: 100 });
});
```

**Fix 3: Use event-based synchronization**

```typescript
// If your system emits events
it('should send notification after user signup', async () => {
  const user = await createUser({ email: 'test@example.com' });
  
  const notificationReceived = new Promise(resolve => {
    eventBus.once('notification:sent', resolve);
  });
  
  await signupUser(user);
  await notificationReceived;
  
  const notifications = await getNotifications(user.id);
  expect(notifications).toHaveLength(1);
});
```

**Verification**:

Run the test in a loop to verify the fix:
```bash
# Run 50 times - should pass every time
for i in {1..50}; do npm test -- --testNamePattern="notification" || exit 1; done
```

**Prevention**:
- Avoid fire-and-forget async operations in testable code
- Use dependency injection for async operations so tests can await them
- Add CI step that runs tests multiple times to catch flakiness early

## Common Flakiness Causes

| Cause | Symptom | Fix |
|-------|---------|-----|
| Race condition | Random failures, worse in CI | Await async operations |
| Shared state | Fails when tests run in different order | Isolate test data |
| Time dependency | Fails at certain times | Mock time/dates |
| External service | Fails with network issues | Mock external calls |
| Resource exhaustion | Fails under parallel execution | Limit concurrency |
| Order dependency | Fails when run alone | Remove test interdependencies |

## Tips & Variations

### Tool-Specific Notes

- **Jest**: Use `--runInBand` to run tests serially and isolate order-dependent flakiness
- **pytest**: Use `pytest-randomly` to detect order-dependent tests
- **CI**: Most CI systems support test retries, but this masks the problem

### Follow-up Prompts

```text
The fix worked but now the test is slow. How can I speed it up while keeping it reliable?
```

```text
How can I add a CI check that detects flaky tests before they're merged?
```

## Related Playbooks

- [Debug from Stack Trace](debug-stack-trace.md)
- [Generate Unit Tests](../testing/generate-unit-tests.md)
- [Test Edge Cases](../testing/test-edge-cases.md)
