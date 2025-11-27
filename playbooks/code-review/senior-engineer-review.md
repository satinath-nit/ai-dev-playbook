# Senior Engineer Code Review

> Get a thorough code review from the perspective of a senior engineer, covering correctness, maintainability, performance, and best practices.

## When to Use

- Before submitting a PR for review
- When you want a second opinion on your implementation
- Learning best practices for a new language or framework
- Reviewing your own code before merging
- When human reviewers are unavailable

## The Prompt

```text
Please review this code as a senior software engineer would. Be thorough but constructive.

**Code to Review**:
```[LANGUAGE]
[CODE]
```

**Context**:
- Purpose: [PURPOSE]
- Language/Framework: [LANGUAGE_FRAMEWORK]
- This code will: [USAGE_CONTEXT]

**Review Focus** (check all that apply):
- [x] Correctness and logic errors
- [x] Code style and readability
- [x] Performance considerations
- [x] Security vulnerabilities
- [x] Error handling
- [x] Testability
- [x] Maintainability
- [ ] [CUSTOM_FOCUS]

Please structure your review as:
1. **Summary**: Overall assessment (1-2 sentences)
2. **Critical Issues**: Must fix before merging
3. **Suggestions**: Improvements that would make the code better
4. **Positive Notes**: What's done well (important for learning)
5. **Questions**: Clarifications needed to complete the review
```

## Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `[LANGUAGE]` | Programming language | `TypeScript`, `Python` |
| `[CODE]` | The code to review | Your function, class, or file |
| `[PURPOSE]` | What the code does | "Handles user authentication" |
| `[LANGUAGE_FRAMEWORK]` | Tech stack | `React/TypeScript`, `FastAPI/Python` |
| `[USAGE_CONTEXT]` | Where/how it's used | "Called on every API request" |
| `[CUSTOM_FOCUS]` | Specific concerns | "Thread safety", "Memory usage" |

## Example

### Input

```text
Please review this code as a senior software engineer would. Be thorough but constructive.

**Code to Review**:
```typescript
import { Request, Response } from 'express';
import jwt from 'jsonwebtoken';
import { db } from './database';

export async function handleLogin(req: Request, res: Response) {
  const { email, password } = req.body;
  
  const user = await db.query(`SELECT * FROM users WHERE email = '${email}'`);
  
  if (user.length == 0) {
    return res.status(400).json({ error: 'User not found' });
  }
  
  if (user[0].password != password) {
    return res.status(400).json({ error: 'Wrong password' });
  }
  
  const token = jwt.sign({ userId: user[0].id }, 'secret123');
  
  res.json({ token: token, user: user[0] });
}
```

**Context**:
- Purpose: Handle user login and return JWT token
- Language/Framework: Express.js with TypeScript
- This code will: Be called when users submit the login form

**Review Focus**:
- [x] Correctness and logic errors
- [x] Code style and readability
- [x] Performance considerations
- [x] Security vulnerabilities
- [x] Error handling
- [x] Testability
- [x] Maintainability
```

### Expected Output

**Summary**: This login handler has several critical security vulnerabilities that must be addressed before deployment. The core logic is present but needs significant hardening.

**Critical Issues**:

1. **SQL Injection Vulnerability** (Security - Critical)
   ```typescript
   // DANGEROUS: User input directly in SQL
   const user = await db.query(`SELECT * FROM users WHERE email = '${email}'`);
   ```
   Fix: Use parameterized queries
   ```typescript
   const user = await db.query('SELECT * FROM users WHERE email = $1', [email]);
   ```

2. **Plain Text Password Comparison** (Security - Critical)
   ```typescript
   if (user[0].password != password)
   ```
   Passwords should be hashed. Use bcrypt:
   ```typescript
   import bcrypt from 'bcrypt';
   const isValid = await bcrypt.compare(password, user[0].passwordHash);
   ```

3. **Hardcoded JWT Secret** (Security - Critical)
   ```typescript
   jwt.sign({ userId: user[0].id }, 'secret123');
   ```
   Fix: Use environment variable
   ```typescript
   jwt.sign({ userId: user[0].id }, process.env.JWT_SECRET!, { expiresIn: '24h' });
   ```

4. **Returning Full User Object** (Security - High)
   ```typescript
   res.json({ token: token, user: user[0] });
   ```
   This likely includes the password hash. Return only needed fields:
   ```typescript
   const { id, email, name } = user[0];
   res.json({ token, user: { id, email, name } });
   ```

**Suggestions**:

1. **Add Input Validation**
   ```typescript
   if (!email || !password) {
     return res.status(400).json({ error: 'Email and password required' });
   }
   ```

2. **Use Consistent Comparison Operators**
   ```typescript
   // Use === instead of ==
   if (user.length === 0)
   ```

3. **Add Token Expiration**
   ```typescript
   jwt.sign({ userId: user[0].id }, secret, { expiresIn: '24h' });
   ```

4. **Consider Rate Limiting**: Login endpoints should have rate limiting to prevent brute force attacks.

5. **Add Try-Catch for Error Handling**
   ```typescript
   try {
     // ... login logic
   } catch (error) {
     console.error('Login error:', error);
     return res.status(500).json({ error: 'Internal server error' });
   }
   ```

**Positive Notes**:
- Good use of async/await
- Appropriate HTTP status codes (400 for client errors)
- Clean function signature with typed parameters

**Questions**:
- Is there a reason passwords aren't hashed in the database?
- What's the expected token lifetime?
- Is this endpoint behind any rate limiting middleware?

**Refactored Version**:
```typescript
import { Request, Response } from 'express';
import jwt from 'jsonwebtoken';
import bcrypt from 'bcrypt';
import { db } from './database';

export async function handleLogin(req: Request, res: Response) {
  try {
    const { email, password } = req.body;
    
    if (!email || !password) {
      return res.status(400).json({ error: 'Email and password are required' });
    }
    
    const users = await db.query(
      'SELECT id, email, name, password_hash FROM users WHERE email = $1',
      [email]
    );
    
    if (users.length === 0) {
      return res.status(401).json({ error: 'Invalid credentials' });
    }
    
    const user = users[0];
    const isValidPassword = await bcrypt.compare(password, user.password_hash);
    
    if (!isValidPassword) {
      return res.status(401).json({ error: 'Invalid credentials' });
    }
    
    const token = jwt.sign(
      { userId: user.id },
      process.env.JWT_SECRET!,
      { expiresIn: '24h' }
    );
    
    return res.json({
      token,
      user: { id: user.id, email: user.email, name: user.name }
    });
  } catch (error) {
    console.error('Login error:', error);
    return res.status(500).json({ error: 'Internal server error' });
  }
}
```

## Tips & Variations

### Tool-Specific Notes

- **GitHub Copilot**: Use `/review` command or paste into Copilot Chat
- **Cursor**: Select code and use Cmd+K with "review this code"
- **Claude/ChatGPT**: Full prompt works best for comprehensive review

### Common Pitfalls

- **AI missing context**: Provide information about how the code is used
- **Too much code at once**: Review in logical chunks (one file or feature)
- **Not specifying focus**: Tell the AI what matters most for your use case

### Follow-up Prompts

For more detail on a specific issue:
```text
Can you explain more about why [ISSUE] is a problem and show me the best practice?
```

To get the refactored code:
```text
Please show me the complete refactored version incorporating all your suggestions.
```

For security-focused review:
```text
Focus specifically on security vulnerabilities. What are all the ways this code could be exploited?
```

### Variations

**Quick Review** for small changes:
```text
Quick review: any issues with this code?
[CODE]
```

**PR Review** format:
```text
Review this PR diff and provide feedback in GitHub PR comment format:
[DIFF]
```

**Learning-focused** review:
```text
Review this code and explain the issues in a way that helps me learn. I'm new to [LANGUAGE/FRAMEWORK].
```

## Related Playbooks

- [Security Review](security-review.md)
- [Performance Review](performance-review.md)
- [API Design Review](api-design-review.md)
