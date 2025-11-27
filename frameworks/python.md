# Python Prompting Guide

> Tips and patterns for getting better AI assistance when working with Python codebases.

## Context to Include

When prompting about Python code, include:

```text
**Python Context**:
- Python version: [VERSION] (e.g., 3.11)
- Framework: [FRAMEWORK] (e.g., FastAPI, Django, Flask)
- Type hints: Yes/No
- Testing: [FRAMEWORK] (e.g., pytest, unittest)
- Async: Yes/No
- Package manager: [TOOL] (e.g., pip, poetry, uv)
```

## Common Patterns

### Function Generation

```text
Create a Python function that [DESCRIPTION].

Requirements:
- Python 3.11+ syntax
- Full type hints (parameters and return type)
- Docstring in Google style
- Handle errors with custom exceptions
- Include example usage in docstring
```

### Class Design

```text
Design a Python class for [PURPOSE].

Requirements:
- Use dataclasses or Pydantic (specify which)
- Include type hints
- Add validation where appropriate
- Include __str__ and __repr__
- Make it immutable if appropriate
```

### API Endpoint (FastAPI)

```text
Create a FastAPI endpoint that [DESCRIPTION].

Requirements:
- Proper request/response models with Pydantic
- Input validation
- Error handling with HTTPException
- Async if doing I/O
- Include OpenAPI documentation (summary, description)
```

## Python-Specific Tips

1. **Specify Python version**: 3.9 vs 3.11 have different features
2. **Mention type hints preference**: Some codebases require strict typing
3. **Async matters**: Specify if you need async/await patterns
4. **Framework conventions**: Django vs FastAPI have very different patterns

## Example Prompts

### Add Type Hints

```text
Add comprehensive type hints to this Python code:
- Use modern syntax (3.10+ union with |)
- Use TypeVar for generics where appropriate
- Add return types to all functions
- Use Optional only when None is valid

[CODE]
```

### Convert to Async

```text
Convert this synchronous code to async:
- Use asyncio patterns
- Replace blocking calls with async equivalents
- Use aiohttp instead of requests
- Maintain the same interface

[CODE]
```

### Create Pydantic Model

```text
Create Pydantic models for this data structure:
- Use Pydantic v2 syntax
- Add field validators where needed
- Include examples in Field()
- Add model_config for JSON serialization

Data structure:
[DESCRIPTION_OR_EXAMPLE]
```

## Anti-Patterns to Avoid

When AI suggests these, ask for alternatives:

- `# type: ignore` comments (fix the type instead)
- Bare `except:` clauses
- Mutable default arguments
- Global variables for state
- `from module import *`
