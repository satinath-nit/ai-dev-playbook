# Contributing to AI Dev Playbook

Thank you for your interest in contributing! This project thrives on community contributions - whether you're adding new playbooks, improving existing ones, or fixing typos.

## Ways to Contribute

### 1. Add a New Playbook

Have you discovered an effective prompt pattern for a coding task? Share it!

**Before you start:**
- Check existing playbooks to avoid duplicates
- Ensure your playbook is tool-agnostic (works in any AI assistant)
- Test your prompt in at least 2 different AI tools

**Steps:**
1. Fork the repository
2. Create a new file in the appropriate `playbooks/` subdirectory
3. Follow the [Playbook Template](#playbook-template) below
4. Submit a pull request

### 2. Improve Existing Playbooks

- Add better examples
- Include tool-specific tips
- Fix unclear instructions
- Add "watch out for" warnings based on your experience

### 3. Add Framework-Specific Guides

Create guides in the `frameworks/` directory for frameworks not yet covered.

### 4. Report Issues

- Playbook doesn't work as expected
- Missing important use cases
- Unclear instructions

## Playbook Template

Use this template when creating new playbooks:

```markdown
# [Playbook Title]

> One-line description of what this playbook helps you do.

## When to Use

Describe the situation or problem this playbook addresses. Be specific about:
- What task you're trying to accomplish
- What kind of codebase or context this works best for
- Any prerequisites or setup needed

## The Prompt

\`\`\`text
[Your prompt template here]

Use [BRACKETS] for variables that users need to fill in.

Example variables:
- [CODE]: The code to analyze
- [LANGUAGE]: Programming language
- [FRAMEWORK]: Framework being used
- [REQUIREMENTS]: Specific requirements or constraints
\`\`\`

## Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `[CODE]` | Description of what to provide | `function add(a, b) { return a + b; }` |
| `[LANGUAGE]` | Description | `TypeScript` |

## Example

### Input

Show a complete example with all variables filled in:

\`\`\`text
[The complete prompt with real values]
\`\`\`

### Context Provided

\`\`\`[language]
[The actual code or context you provided]
\`\`\`

### Expected Output

Describe or show what kind of response to expect:

\`\`\`[language]
[Example output]
\`\`\`

## Tips & Variations

### Tool-Specific Notes

- **GitHub Copilot**: Any Copilot-specific tips
- **Cursor**: Any Cursor-specific tips
- **Claude/ChatGPT**: Any tips for chat-based tools

### Common Pitfalls

- List things that might go wrong
- How to fix them

### Follow-up Prompts

If the initial response needs refinement:

\`\`\`text
[Follow-up prompt to improve the output]
\`\`\`

### Variations

- Variation for different use cases
- Simpler version for quick tasks
- More detailed version for complex scenarios

## Related Playbooks

- [Related Playbook 1](../category/related-playbook-1.md)
- [Related Playbook 2](../category/related-playbook-2.md)
```

## File Naming Conventions

- Use lowercase with hyphens: `generate-unit-tests.md`
- Be descriptive but concise
- Match the playbook title

## Directory Structure

```
playbooks/
├── testing/           # Test generation and testing strategies
├── debugging/         # Bug finding and fixing
├── refactoring/       # Code improvement and restructuring
├── code-review/       # Code review assistance
├── documentation/     # Documentation generation
├── migration/         # Upgrades and migrations
└── architecture/      # System design and architecture

patterns/              # Reusable prompt engineering patterns
frameworks/            # Framework-specific guides
examples/              # Complete example projects
```

## Quality Guidelines

### Do

- Test your playbook in multiple AI tools before submitting
- Include realistic, working examples
- Add "watch out for" tips based on real experience
- Keep prompts focused on one task
- Use clear, simple language

### Don't

- Submit untested prompts
- Include tool-specific syntax that won't work elsewhere
- Add overly complex prompts that try to do everything
- Copy prompts from proprietary sources without attribution
- Include sensitive or proprietary code in examples

## Pull Request Process

1. **Fork & Branch**: Create a branch from `main`
2. **Make Changes**: Follow the templates and guidelines
3. **Test**: Verify your playbook works in at least 2 AI tools
4. **Submit PR**: Include:
   - What the playbook does
   - Which AI tools you tested it with
   - Any special considerations

### PR Title Format

- New playbook: `Add: [playbook-name] playbook`
- Improvement: `Improve: [playbook-name] - [what you improved]`
- Fix: `Fix: [playbook-name] - [what you fixed]`
- Docs: `Docs: [what you updated]`

## Code of Conduct

Please read and follow our [Code of Conduct](CODE_OF_CONDUCT.md).

## Questions?

- Open a [Discussion](https://github.com/YOUR_USERNAME/ai-dev-playbook/discussions) for questions
- Open an [Issue](https://github.com/YOUR_USERNAME/ai-dev-playbook/issues) for bugs or feature requests

## License

By contributing, you agree that your contributions will be licensed under the Apache License 2.0.

---

Thank you for helping make AI coding assistants more useful for everyone!
