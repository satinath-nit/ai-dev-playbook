# Context Anchoring Pattern

> Keep AI focused on your specific codebase, conventions, and constraints to prevent hallucinations and generic responses.

## What It Is

Context Anchoring is a technique to ground AI responses in your specific codebase and requirements. Without anchoring, AI tends to generate generic solutions that may not fit your project's patterns, use libraries you don't have, or ignore your conventions.

## When to Use

- Working in an established codebase with specific patterns
- When AI keeps suggesting libraries or approaches you can't use
- When responses ignore your project's conventions
- When AI "hallucinates" APIs or methods that don't exist
- When you need solutions that match existing code style

## The Pattern

```text
**Context Anchoring Block** (include at the start of your prompt):

I'm working in a codebase with these constraints:
- Language: [LANGUAGE] [VERSION]
- Framework: [FRAMEWORK] [VERSION]
- Available libraries: [LIBRARIES]
- NOT available: [UNAVAILABLE]
- Code style: [STYLE_NOTES]
- Existing patterns: [PATTERNS]

Important: Only use libraries and APIs that exist in the versions specified. 
Do not suggest installing new dependencies unless I ask.

---

[YOUR_ACTUAL_QUESTION]
```

## Example

### Without Context Anchoring

**Prompt**:
```text
How do I make an HTTP request in Python?
```

**Result**: AI might suggest `requests`, `httpx`, `aiohttp`, or even `urllib3` - any of which might not be in your project.

### With Context Anchoring

**Prompt**:
```text
I'm working in a codebase with these constraints:
- Language: Python 3.9
- Framework: FastAPI
- Available libraries: httpx (async), pydantic
- NOT available: requests, aiohttp
- Code style: All HTTP calls should be async
- Existing patterns: We use dependency injection for HTTP clients

Important: Only use libraries and APIs that exist. Do not suggest new dependencies.

---

How do I make an HTTP request to an external API from within a FastAPI endpoint?
```

**Result**: AI provides a solution using `httpx` with async/await, following FastAPI patterns with dependency injection.

## Anchoring Techniques

### 1. Library Anchoring
```text
Available libraries (do not suggest others):
- React 18
- TanStack Query (not React Query v3)
- Zustand (not Redux)
- Tailwind CSS (not styled-components)
```

### 2. Version Anchoring
```text
We're using Node.js 18 LTS. Only use features available in this version.
Do not use features from Node.js 20+ like the built-in test runner.
```

### 3. Pattern Anchoring
```text
Our codebase follows these patterns:
- Repository pattern for data access
- Services never call repositories directly, only through use cases
- All errors are custom error classes extending BaseError
- We use Result<T, E> instead of throwing exceptions

Match these patterns in your solution.
```

### 4. Style Anchoring
```text
Match this code style:
- 2-space indentation
- Single quotes for strings
- No semicolons (JavaScript)
- Function components only (React)
- Descriptive variable names, no abbreviations
```

### 5. File Structure Anchoring
```text
Our project structure:
src/
  components/    # React components
  hooks/         # Custom hooks
  services/      # API calls
  utils/         # Pure utility functions
  types/         # TypeScript types

Place new code in the appropriate directory.
```

## Full Example

```text
**Project Context**:
- Stack: Next.js 14 (App Router), TypeScript, Prisma, PostgreSQL
- UI: Tailwind CSS, shadcn/ui components
- State: Server components by default, Zustand for client state
- Auth: NextAuth.js v5

**Conventions**:
- Use server actions for mutations
- Use React Server Components unless interactivity needed
- Error handling: return { success: false, error: string } not throw
- All database calls go through Prisma, no raw SQL

**Available shadcn components**: Button, Card, Input, Form, Dialog, Table

Do not suggest:
- Client components unless necessary
- Redux or other state libraries
- Different UI libraries
- Direct database connections

---

I need to create a user settings page where users can update their profile (name, email) and change their password. Show me how to structure this with proper form handling and validation.
```

## When AI Ignores Context

If AI still suggests things outside your constraints, use these follow-ups:

```text
That solution uses [LIBRARY] which I mentioned is not available. 
Please revise using only [AVAILABLE_LIBRARY].
```

```text
This doesn't match our pattern of [PATTERN]. 
Please refactor to follow that pattern.
```

```text
Remember: we're on [FRAMEWORK] version [X], which doesn't have [FEATURE].
What's the alternative approach for this version?
```

## Creating a Project Context File

For frequently used context, create a file you can paste:

```markdown
<!-- project-context.md -->
# Project Context for AI Prompts

## Stack
- Python 3.11, FastAPI, SQLAlchemy 2.0, Pydantic v2
- PostgreSQL 15, Redis
- pytest for testing

## Patterns
- Repository pattern for data access
- Dependency injection via FastAPI's Depends
- Async everywhere

## Conventions
- Type hints required
- Docstrings in Google style
- 88 char line length (Black)

## Do Not Use
- Django ORM
- requests library (use httpx)
- print statements (use logging)
```

## Tips

1. **Be specific about versions**: "React" vs "React 18" makes a big difference
2. **List what's NOT available**: Prevents suggestions you can't use
3. **Include existing patterns**: AI will match your style better
4. **Update context as project evolves**: Keep your context file current

## Anti-Patterns

- **Too much context**: Don't paste your entire codebase; be selective
- **Outdated context**: Old version numbers lead to deprecated suggestions
- **Vague constraints**: "Modern JavaScript" is less useful than "ES2022, no experimental features"

## Related Patterns

- [Chain of Thought](chain-of-thought.md)
- [Iterative Refinement](iterative-refinement.md)
- [Spec-First Implementation](spec-first-implementation.md)
