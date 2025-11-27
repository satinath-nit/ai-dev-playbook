# AI Dev Playbook

A curated collection of **tested prompt patterns and workflows** for real coding tasks that work in ANY AI coding assistant - GitHub Copilot, Cursor, Windsurf, ChatGPT, Claude, and more.

## Why This Exists

AI coding assistants are powerful, but getting great results requires knowing how to ask. This repository provides battle-tested prompt templates and step-by-step workflows that you can copy, paste, and use immediately.

**No installation. No configuration. Just copy and paste.**

## Quick Start

1. Browse the [playbooks](#playbooks) below
2. Find one that matches your task
3. Copy the prompt template
4. Paste into your AI tool of choice
5. Fill in the variables with your specific context

## Playbooks

### Testing

| Playbook | Description |
|----------|-------------|
| [Generate Unit Tests](playbooks/testing/generate-unit-tests.md) | Create comprehensive unit tests for existing code |
| [Test Edge Cases](playbooks/testing/test-edge-cases.md) | Identify and test edge cases and boundary conditions |
| [Mock External Dependencies](playbooks/testing/mock-dependencies.md) | Generate mocks for APIs, databases, and services |
| [Property-Based Testing](playbooks/testing/property-based-testing.md) | Create property-based tests for complex logic |

### Debugging

| Playbook | Description |
|----------|-------------|
| [Debug from Stack Trace](playbooks/debugging/debug-stack-trace.md) | Analyze error stack traces and find root causes |
| [Fix Flaky Tests](playbooks/debugging/fix-flaky-tests.md) | Diagnose and fix intermittent test failures |
| [Memory Leak Detection](playbooks/debugging/memory-leak-detection.md) | Identify potential memory leaks in code |
| [Performance Bottleneck](playbooks/debugging/performance-bottleneck.md) | Find and fix performance issues |

### Refactoring

| Playbook | Description |
|----------|-------------|
| [Extract Module](playbooks/refactoring/extract-module.md) | Break down large files into smaller modules |
| [Break Up God Class](playbooks/refactoring/break-up-god-class.md) | Decompose oversized classes responsibly |
| [Improve Testability](playbooks/refactoring/improve-testability.md) | Refactor code to be more testable |
| [Remove Code Duplication](playbooks/refactoring/remove-duplication.md) | Identify and eliminate duplicate code |

### Code Review

| Playbook | Description |
|----------|-------------|
| [Senior Engineer Review](playbooks/code-review/senior-engineer-review.md) | Review code like a senior engineer |
| [Security Review](playbooks/code-review/security-review.md) | Check for security vulnerabilities |
| [Performance Review](playbooks/code-review/performance-review.md) | Review for performance issues |
| [API Design Review](playbooks/code-review/api-design-review.md) | Evaluate API design decisions |

### Documentation

| Playbook | Description |
|----------|-------------|
| [Generate API Docs](playbooks/documentation/generate-api-docs.md) | Create comprehensive API documentation |
| [Write README](playbooks/documentation/write-readme.md) | Generate a professional README |
| [Document Architecture](playbooks/documentation/document-architecture.md) | Create architecture documentation |
| [Add Code Comments](playbooks/documentation/add-code-comments.md) | Add meaningful comments to complex code |

### Migration

| Playbook | Description |
|----------|-------------|
| [Upgrade Dependencies](playbooks/migration/upgrade-dependencies.md) | Safely upgrade package versions |
| [Framework Migration](playbooks/migration/framework-migration.md) | Migrate between frameworks |
| [Database Schema Migration](playbooks/migration/database-schema-migration.md) | Plan and execute schema changes |
| [API Version Migration](playbooks/migration/api-version-migration.md) | Migrate to new API versions |

### Architecture

| Playbook | Description |
|----------|-------------|
| [Design System Architecture](playbooks/architecture/design-system-architecture.md) | Plan system architecture |
| [Microservices Decomposition](playbooks/architecture/microservices-decomposition.md) | Break monolith into services |
| [Event-Driven Design](playbooks/architecture/event-driven-design.md) | Design event-driven systems |
| [API Gateway Design](playbooks/architecture/api-gateway-design.md) | Design API gateway patterns |

## Patterns

Reusable prompt engineering patterns that work across different tasks:

| Pattern | Description |
|---------|-------------|
| [Chain of Thought](patterns/chain-of-thought.md) | Step-by-step reasoning for complex problems |
| [Rubber Duck Debugging](patterns/rubber-duck-debugging.md) | Use AI as a debugging partner |
| [Spec-First Implementation](patterns/spec-first-implementation.md) | Define specs before coding |
| [Iterative Refinement](patterns/iterative-refinement.md) | Progressively improve AI output |
| [Context Anchoring](patterns/context-anchoring.md) | Keep AI focused on your codebase |

## Framework-Specific Guides

Tips for using these playbooks with specific frameworks:

| Framework | Guide |
|-----------|-------|
| [React](frameworks/react.md) | React-specific prompting tips |
| [Node.js](frameworks/nodejs.md) | Node.js backend patterns |
| [Python](frameworks/python.md) | Python and Django/FastAPI tips |
| [Go](frameworks/go.md) | Go-specific patterns |
| [Java/Spring](frameworks/java-spring.md) | Spring Boot patterns |

## Tool Compatibility

These playbooks are designed to work with any AI coding assistant:

| Tool | How to Use |
|------|------------|
| **GitHub Copilot** | Paste into Copilot Chat |
| **Cursor** | Paste into chat or save as custom command |
| **Windsurf** | Use in Cascade chat |
| **ChatGPT** | Paste directly into conversation |
| **Claude** | Paste directly into conversation |
| **Cline** | Use in VS Code chat |

## Playbook Structure

Each playbook follows a consistent format:

```markdown
# Playbook Title

## When to Use
Describes the situation where this playbook is helpful.

## The Prompt
The actual prompt template with [VARIABLES] to fill in.

## Variables
Explanation of each variable and what to provide.

## Example
A complete example with filled-in variables.

## Expected Output
What kind of response to expect.

## Tips & Variations
- Tool-specific notes
- Common pitfalls to avoid
- Follow-up prompts for refinement

## Related Playbooks
Links to related playbooks.
```

## Contributing

We welcome contributions! See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

Ways to contribute:
- Add new playbooks for tasks you've found effective prompts for
- Improve existing playbooks with better examples or tips
- Add framework-specific guides
- Fix typos and improve clarity
- Share your success stories

## Community

- [Discussions](https://github.com/YOUR_USERNAME/ai-dev-playbook/discussions) - Ask questions and share ideas
- [Issues](https://github.com/YOUR_USERNAME/ai-dev-playbook/issues) - Report problems or request new playbooks

## License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

Inspired by the developer community's collective wisdom in working with AI coding assistants. Special thanks to all contributors who share their effective prompts and workflows.

---

**Found this helpful?** Give it a star and share with your team!
