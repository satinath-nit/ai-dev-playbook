# Security Code Review

> Review code for security vulnerabilities, following OWASP guidelines and security best practices.

## When to Use

- Reviewing code that handles user input
- Code that deals with authentication/authorization
- Before deploying to production
- Reviewing code that handles sensitive data
- API endpoint implementations

## The Prompt

```text
Please perform a security-focused code review on this code.

**Code to Review**:
```[LANGUAGE]
[CODE]
```

**Context**:
- This code: [WHAT_IT_DOES]
- It handles: [DATA_TYPES] (e.g., user input, passwords, PII, financial data)
- Environment: [ENVIRONMENT] (e.g., public API, internal service, admin panel)

**Check for**:
- [ ] Injection vulnerabilities (SQL, NoSQL, Command, LDAP)
- [ ] Authentication/Authorization flaws
- [ ] Sensitive data exposure
- [ ] Security misconfiguration
- [ ] Cross-Site Scripting (XSS)
- [ ] Insecure deserialization
- [ ] Using components with known vulnerabilities
- [ ] Insufficient logging/monitoring
- [ ] [CUSTOM_CONCERNS]

For each vulnerability found:
1. Explain the risk and potential impact
2. Show how it could be exploited
3. Provide the secure fix
4. Rate severity (Critical/High/Medium/Low)
```

## Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `[LANGUAGE]` | Programming language | `Python`, `JavaScript` |
| `[CODE]` | Code to review | Your function or endpoint |
| `[WHAT_IT_DOES]` | Purpose of the code | "Handles user login" |
| `[DATA_TYPES]` | What data it processes | "Passwords, email addresses" |
| `[ENVIRONMENT]` | Where it runs | "Public-facing API" |
| `[CUSTOM_CONCERNS]` | Specific concerns | "GDPR compliance" |

## Example

### Input

```text
Please perform a security-focused code review on this code.

**Code to Review**:
```javascript
const express = require('express');
const mysql = require('mysql');
const app = express();

app.use(express.json());

const db = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: 'password123',
  database: 'users'
});

app.post('/login', (req, res) => {
  const { username, password } = req.body;
  
  const query = `SELECT * FROM users WHERE username = '${username}' AND password = '${password}'`;
  
  db.query(query, (err, results) => {
    if (err) {
      res.status(500).send(err.message);
      return;
    }
    
    if (results.length > 0) {
      res.cookie('user', username);
      res.json({ success: true, user: results[0] });
    } else {
      res.status(401).json({ error: 'Invalid credentials' });
    }
  });
});

app.get('/user/:id', (req, res) => {
  const query = `SELECT * FROM users WHERE id = ${req.params.id}`;
  
  db.query(query, (err, results) => {
    if (results.length > 0) {
      res.json(results[0]);
    }
  });
});

app.listen(3000);
```

**Context**:
- This code: Handles user authentication and profile retrieval
- It handles: Usernames, passwords, user profile data
- Environment: Public-facing API
```

### Expected Output

## Security Review Results

### Critical Vulnerabilities

#### 1. SQL Injection (Critical)

**Location**: Lines 15, 30
```javascript
const query = `SELECT * FROM users WHERE username = '${username}' AND password = '${password}'`;
```

**Risk**: Complete database compromise. Attacker can read, modify, or delete any data.

**Exploitation**:
```
Username: admin' OR '1'='1' --
Password: anything
```
This bypasses authentication entirely.

**Fix**:
```javascript
const query = 'SELECT * FROM users WHERE username = ? AND password_hash = ?';
db.query(query, [username, hashedPassword], (err, results) => {
  // ...
});
```

---

#### 2. Plain Text Password Storage (Critical)

**Location**: Line 15
```javascript
AND password = '${password}'
```

**Risk**: If database is breached, all passwords are immediately exposed.

**Fix**:
```javascript
const bcrypt = require('bcrypt');

// When storing password
const hash = await bcrypt.hash(password, 12);

// When verifying
const isValid = await bcrypt.compare(password, user.password_hash);
```

---

#### 3. Hardcoded Database Credentials (Critical)

**Location**: Lines 7-10
```javascript
user: 'root',
password: 'password123',
```

**Risk**: Credentials exposed in source code, version control, and logs.

**Fix**:
```javascript
const db = mysql.createConnection({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME
});
```

---

### High Vulnerabilities

#### 4. Sensitive Data Exposure in Response (High)

**Location**: Line 22
```javascript
res.json({ success: true, user: results[0] });
```

**Risk**: Returns entire user record including password hash.

**Fix**:
```javascript
const { id, username, email } = results[0];
res.json({ success: true, user: { id, username, email } });
```

---

#### 5. Error Message Information Disclosure (High)

**Location**: Line 17
```javascript
res.status(500).send(err.message);
```

**Risk**: Database error messages may reveal schema, query structure, or system information.

**Fix**:
```javascript
console.error('Database error:', err);
res.status(500).json({ error: 'Internal server error' });
```

---

#### 6. Insecure Cookie (High)

**Location**: Line 21
```javascript
res.cookie('user', username);
```

**Risk**: Cookie can be stolen via XSS, modified by client, sent over HTTP.

**Fix**:
```javascript
const jwt = require('jsonwebtoken');
const token = jwt.sign({ userId: user.id }, process.env.JWT_SECRET, { expiresIn: '24h' });
res.cookie('token', token, {
  httpOnly: true,
  secure: true,
  sameSite: 'strict',
  maxAge: 86400000
});
```

---

### Medium Vulnerabilities

#### 7. Missing Authorization Check (Medium)

**Location**: `/user/:id` endpoint
```javascript
app.get('/user/:id', (req, res) => {
```

**Risk**: Any user can access any other user's data (IDOR vulnerability).

**Fix**:
```javascript
app.get('/user/:id', authenticate, (req, res) => {
  if (req.user.id !== parseInt(req.params.id) && !req.user.isAdmin) {
    return res.status(403).json({ error: 'Forbidden' });
  }
  // ...
});
```

---

#### 8. Missing Rate Limiting (Medium)

**Risk**: Login endpoint vulnerable to brute force attacks.

**Fix**:
```javascript
const rateLimit = require('express-rate-limit');

const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5,
  message: 'Too many login attempts'
});

app.post('/login', loginLimiter, (req, res) => {
```

---

### Summary

| Severity | Count | Issues |
|----------|-------|--------|
| Critical | 3 | SQL Injection, Plain Text Passwords, Hardcoded Credentials |
| High | 3 | Data Exposure, Error Disclosure, Insecure Cookie |
| Medium | 2 | Missing AuthZ, No Rate Limiting |

**Recommendation**: Do not deploy this code until all Critical and High issues are fixed.

## Tips & Variations

### OWASP Top 10 Checklist

Use this as a systematic checklist:
1. Injection
2. Broken Authentication
3. Sensitive Data Exposure
4. XML External Entities (XXE)
5. Broken Access Control
6. Security Misconfiguration
7. Cross-Site Scripting (XSS)
8. Insecure Deserialization
9. Using Components with Known Vulnerabilities
10. Insufficient Logging & Monitoring

### Follow-up Prompts

```text
Show me the complete secure version of this code with all fixes applied.
```

```text
What security headers should I add to this Express app?
```

```text
How would I add proper logging for security events in this code?
```

## Related Playbooks

- [Senior Engineer Review](senior-engineer-review.md)
- [API Design Review](api-design-review.md)
- [Performance Review](performance-review.md)
