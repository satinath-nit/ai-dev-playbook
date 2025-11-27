# React Prompting Guide

> Tips and patterns for getting better AI assistance when working with React codebases.

## Context to Include

When prompting about React code, include:

```text
**React Context**:
- React version: [VERSION] (e.g., 18.2)
- State management: [LIBRARY] (e.g., Zustand, Redux, Context)
- Styling: [APPROACH] (e.g., Tailwind, CSS Modules, styled-components)
- Data fetching: [LIBRARY] (e.g., TanStack Query, SWR, fetch)
- Component style: [STYLE] (e.g., functional only, hooks)
- TypeScript: Yes/No
```

## Common Patterns

### Component Generation

```text
Create a React component for [DESCRIPTION].

Requirements:
- Functional component with TypeScript
- Use [STATE_MANAGEMENT] for state
- Style with [STYLING_APPROACH]
- Include proper prop types
- Handle loading and error states
- Make it accessible (ARIA labels, keyboard navigation)
```

### Hook Creation

```text
Create a custom React hook called use[NAME] that:
- [FUNCTIONALITY]
- Handles cleanup properly
- Is TypeScript typed
- Includes JSDoc documentation

Show usage example.
```

### Performance Optimization

```text
Review this React component for performance issues:
```tsx
[COMPONENT_CODE]
```

Check for:
- Unnecessary re-renders
- Missing memoization (useMemo, useCallback, React.memo)
- Expensive calculations in render
- Large component trees that should be split
```

## React-Specific Tips

1. **Specify React version**: React 18 has different patterns than React 17
2. **Mention Server Components**: If using Next.js App Router, specify client vs server
3. **State management matters**: Results differ significantly based on your state library
4. **Include TypeScript types**: Better suggestions when types are provided

## Example Prompts

### Convert Class to Functional

```text
Convert this class component to a functional component with hooks:
- Preserve all functionality
- Use appropriate hooks (useState, useEffect, useCallback)
- Maintain the same prop interface
- Add TypeScript types

[CLASS_COMPONENT]
```

### Add Error Boundary

```text
Create an error boundary component that:
- Catches errors in child components
- Shows a fallback UI with error message
- Has a "retry" button
- Logs errors to console (placeholder for error service)
- Is TypeScript typed
```

### Optimize Re-renders

```text
This component re-renders too often. Optimize it:
- Identify what's causing re-renders
- Apply appropriate memoization
- Split into smaller components if needed
- Explain each optimization

[COMPONENT_CODE]
```

## Anti-Patterns to Avoid

When AI suggests these, ask for alternatives:

- Class components (unless you specifically need them)
- `any` types in TypeScript
- Inline function definitions in JSX (when performance matters)
- Direct DOM manipulation
- Mutating state directly
