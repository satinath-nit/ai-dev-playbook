# Debug from Stack Trace

> Analyze error stack traces to identify root causes and get actionable fix suggestions.

## When to Use

- You have an error with a stack trace you don't understand
- You're debugging a production issue from logs
- You need to quickly understand what went wrong
- You want to learn from the error to prevent similar issues

## The Prompt

```text
I have an error that I need help debugging. Please analyze this stack trace and help me understand:

1. What is the root cause of this error?
2. What is the execution flow that led to this error?
3. What are the most likely fixes?
4. How can I prevent this type of error in the future?

**Error/Stack Trace**:
```
[STACK_TRACE]
```

**Relevant Code** (if available):
```[LANGUAGE]
[CODE]
```

**Context**:
- Language/Framework: [LANGUAGE_FRAMEWORK]
- What I was trying to do: [ACTION]
- When it happens: [TRIGGER]

Please explain in a way that helps me understand the underlying issue, not just fix this specific instance.
```

## Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `[STACK_TRACE]` | The full error message and stack trace | The error output from your console |
| `[CODE]` | Relevant code around the error | The function or file mentioned in the trace |
| `[LANGUAGE_FRAMEWORK]` | Tech stack | `Node.js/Express`, `Python/Django` |
| `[ACTION]` | What you were doing | "Submitting a form", "Making an API call" |
| `[TRIGGER]` | When the error occurs | "Only with certain inputs", "Randomly" |

## Example

### Input

```text
I have an error that I need help debugging. Please analyze this stack trace and help me understand:

1. What is the root cause of this error?
2. What is the execution flow that led to this error?
3. What are the most likely fixes?
4. How can I prevent this type of error in the future?

**Error/Stack Trace**:
```
TypeError: Cannot read properties of undefined (reading 'map')
    at UserList (/app/src/components/UserList.tsx:15:23)
    at renderWithHooks (/app/node_modules/react-dom/cjs/react-dom.development.js:14985:18)
    at mountIndeterminateComponent (/app/node_modules/react-dom/cjs/react-dom.development.js:17811:13)
    at beginWork (/app/node_modules/react-dom/cjs/react-dom.development.js:19049:16)
    at HTMLUnknownElement.callCallback (/app/node_modules/react-dom/cjs/react-dom.development.js:3945:14)
```

**Relevant Code**:
```typescript
// UserList.tsx
import { useEffect, useState } from 'react';
import { fetchUsers } from '../api/users';

interface User {
  id: string;
  name: string;
}

export function UserList() {
  const [users, setUsers] = useState<User[]>();

  useEffect(() => {
    fetchUsers().then(setUsers);
  }, []);

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

**Context**:
- Language/Framework: React with TypeScript
- What I was trying to do: Rendering a list of users from an API
- When it happens: On initial page load
```

### Expected Output

The AI should explain:

**Root Cause**: The `users` state is initialized as `undefined` (no default value), and the component tries to call `.map()` on it before the API response arrives.

**Execution Flow**:
1. Component mounts
2. `useState<User[]>()` initializes `users` as `undefined`
3. `useEffect` triggers but `fetchUsers()` is async
4. Render happens immediately with `users` still `undefined`
5. `users.map()` throws because `undefined` has no `.map()` method

**Fixes**:

```typescript
// Fix 1: Initialize with empty array
const [users, setUsers] = useState<User[]>([]);

// Fix 2: Conditional rendering
return (
  <ul>
    {users?.map(user => (
      <li key={user.id}>{user.name}</li>
    ))}
  </ul>
);

// Fix 3: Loading state (recommended)
const [users, setUsers] = useState<User[]>([]);
const [loading, setLoading] = useState(true);

useEffect(() => {
  fetchUsers()
    .then(setUsers)
    .finally(() => setLoading(false));
}, []);

if (loading) return <div>Loading...</div>;
```

**Prevention**: Always initialize state with a sensible default value, especially for arrays and objects that will be iterated over.

## Tips & Variations

### Tool-Specific Notes

- **GitHub Copilot**: Paste the error in Copilot Chat with `/explain` prefix
- **Cursor**: Use Cmd+L to open chat with the error selected
- **Claude/ChatGPT**: Include as much context as possible for better analysis

### Common Pitfalls

- **Not including enough context**: Always include the relevant code, not just the stack trace
- **Truncated stack traces**: Include the full trace, the root cause is often at the bottom
- **Missing environment info**: Mention Node version, browser, OS if relevant

### Follow-up Prompts

If the explanation is unclear:
```text
Can you explain what [SPECIFIC_TERM] means in simpler terms?
```

If you need more fix options:
```text
What are other ways to fix this? I can't use [CONSTRAINT] because [REASON].
```

To understand the deeper issue:
```text
Why does [LANGUAGE/FRAMEWORK] behave this way? Is this a common pattern issue?
```

### Variations

**Quick version** for simple errors:
```text
What's causing this error and how do I fix it?
[STACK_TRACE]
```

**Production debugging** version:
```text
This error is happening in production. I need to:
1. Understand the immediate cause
2. Assess the impact/severity
3. Find a quick fix
4. Plan a proper long-term solution

Error: [STACK_TRACE]
Frequency: [HOW_OFTEN]
Affected users: [SCOPE]
```

## Related Playbooks

- [Fix Flaky Tests](fix-flaky-tests.md)
- [Performance Bottleneck](performance-bottleneck.md)
- [Memory Leak Detection](memory-leak-detection.md)
