# Iterative Refinement Pattern

> Progressively improve AI output through targeted follow-up prompts instead of trying to get perfect results in one shot.

## What It Is

Iterative Refinement treats AI interaction as a conversation rather than a single query. Start with a basic request, then refine the output through focused follow-up prompts. This often produces better results than trying to specify everything upfront.

## When to Use

- Complex tasks that are hard to specify completely upfront
- When the first response is close but not quite right
- When you're not sure exactly what you want until you see options
- When working on creative or design tasks
- When the AI misunderstood part of your request

## The Pattern

```text
**Round 1: Initial Request**
[BASIC_REQUEST]

**Round 2: Refine specific aspects**
That's good, but please:
- Change [ASPECT_1] to [NEW_REQUIREMENT]
- Keep [ASPECT_2] the same
- Add [MISSING_ELEMENT]

**Round 3: Polish**
Almost perfect. Just:
- [FINAL_ADJUSTMENT]
```

## Example

### Without Iteration (One-Shot)

**Prompt**:
```text
Write a React component for a user profile card that shows the user's avatar, name, 
email, role badge, join date, last active time, bio, social links, edit button 
(only for own profile), follow/unfollow button (for other profiles), uses Tailwind CSS, 
is responsive, has hover effects, handles loading state, handles error state, 
is accessible, and matches our design system.
```

**Result**: Often overwhelming, misses requirements, or produces bloated code.

### With Iteration

**Round 1: Core functionality**
```text
Create a React component for a user profile card with:
- Avatar, name, and email
- Uses Tailwind CSS
```

**AI Response**: Clean, simple card component

**Round 2: Add features**
```text
Good! Now add:
- A role badge (admin, moderator, user)
- Join date and last active time
- Make it responsive (stack on mobile)
```

**AI Response**: Updated component with new features

**Round 3: Interactivity**
```text
Now add:
- Edit button that only shows if `isOwnProfile` prop is true
- Follow/Unfollow button for other profiles
- Loading skeleton state
```

**AI Response**: Component with conditional rendering

**Round 4: Polish**
```text
Final touches:
- Add subtle hover effect on the card
- Ensure it's accessible (proper ARIA labels)
- Add error state for failed avatar load
```

**Result**: Well-structured component built incrementally, easier to understand and modify.

## Refinement Techniques

### 1. Aspect-Specific Refinement
```text
The logic is correct, but improve the error handling to:
- Use custom error types
- Add retry logic
- Log errors properly
```

### 2. Style Refinement
```text
Keep the functionality, but refactor to:
- Use more descriptive variable names
- Extract the validation logic into a separate function
- Add JSDoc comments
```

### 3. Scope Adjustment
```text
This is more complex than needed. Simplify by:
- Removing the caching logic
- Using a simpler data structure
- Focusing only on the happy path for now
```

### 4. Alternative Exploration
```text
Show me 2 alternative approaches:
1. Using recursion instead of iteration
2. Using a functional style with reduce
```

### 5. Constraint Addition
```text
Good solution, but we have these constraints:
- Can't use async/await (legacy codebase)
- Must work in Node.js 14
- Should not add new dependencies
```

## Effective Follow-Up Phrases

**Keep what's good**:
- "Keep the [X] but change [Y]"
- "The [part] is perfect, don't modify it"
- "Build on this, adding..."

**Fix specific issues**:
- "The [part] doesn't handle [edge case]"
- "This would fail when [scenario]"
- "The [section] is too [verbose/complex/slow]"

**Request alternatives**:
- "Show me a different approach that..."
- "What if we used [pattern] instead?"
- "How would this look with [constraint]?"

**Clarify intent**:
- "I meant [X], not [Y]"
- "To clarify, the requirement is..."
- "Let me rephrase: I need..."

## Anti-Patterns

### Don't: Restart from scratch each time
```text
❌ "Forget everything. Write a new component that..."
```
You lose the good parts of previous iterations.

### Don't: Be vague about what to change
```text
❌ "Make it better"
❌ "This isn't quite right"
```
Be specific about what needs improvement.

### Don't: Change too many things at once
```text
❌ "Change the styling, add error handling, refactor to hooks, and make it faster"
```
Focus on one aspect per iteration.

## When to Start Over

Sometimes iteration isn't working. Start fresh when:
- The fundamental approach is wrong
- You've iterated 5+ times without convergence
- The code has become a patchwork of fixes
- You now understand the problem better and can specify it clearly

```text
Let's take a different approach. Instead of [current approach], 
let's try [new approach] because [reason].
```

## Tips

1. **Save good versions**: Copy responses you like before iterating further
2. **Be specific**: "Make the button blue" not "improve the styling"
3. **One thing at a time**: Focus each iteration on a single aspect
4. **Acknowledge what's good**: Helps AI understand what to preserve
5. **Know when to stop**: Diminishing returns after 3-4 iterations

## Related Patterns

- [Chain of Thought](chain-of-thought.md)
- [Context Anchoring](context-anchoring.md)
- [Spec-First Implementation](spec-first-implementation.md)
