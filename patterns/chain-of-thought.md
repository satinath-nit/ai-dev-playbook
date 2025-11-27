# Chain of Thought Pattern

> Guide AI through step-by-step reasoning for complex coding problems to get more accurate and well-thought-out solutions.

## What It Is

Chain of Thought (CoT) prompting asks the AI to break down its reasoning into explicit steps before providing a solution. This leads to better results for complex problems where jumping straight to an answer often produces errors.

## When to Use

- Complex algorithmic problems
- Debugging intricate issues
- Architecture decisions with multiple trade-offs
- Code that involves multiple interacting components
- Any problem where "thinking out loud" would help a human

## The Pattern

```text
[PROBLEM_DESCRIPTION]

Please think through this step by step:

1. First, understand and restate the problem
2. Identify the key constraints and requirements
3. Consider possible approaches
4. Evaluate trade-offs of each approach
5. Choose the best approach and explain why
6. Implement the solution
7. Verify the solution handles edge cases

Show your reasoning at each step.
```

## Example

### Without Chain of Thought

**Prompt**:
```text
Write a function to find the longest palindromic substring in a string.
```

**Result**: Often produces a working but suboptimal O(n³) solution without considering better approaches.

### With Chain of Thought

**Prompt**:
```text
Write a function to find the longest palindromic substring in a string.

Please think through this step by step:

1. First, understand what makes a substring palindromic
2. Consider the brute force approach and its complexity
3. Think about how we can optimize
4. Consider the "expand around center" approach
5. Implement the most efficient solution you can
6. Verify it handles edge cases (empty string, single char, all same chars)

Show your reasoning at each step.
```

**Result**: AI walks through the reasoning, considers multiple approaches, and typically produces an O(n²) solution with proper edge case handling.

### Full Example Output

```text
Let me think through this step by step:

**1. Understanding palindromes**
A palindrome reads the same forwards and backwards. For substrings, we need to find the longest contiguous sequence with this property.

**2. Brute force approach**
- Check every possible substring: O(n²) substrings
- For each, check if palindrome: O(n)
- Total: O(n³) - too slow for large strings

**3. Optimization insight**
A palindrome expands from its center. If we know a palindrome exists, we can try to expand it. Centers can be:
- A single character (odd-length palindromes): n centers
- Between two characters (even-length palindromes): n-1 centers

**4. Expand around center approach**
- For each of the 2n-1 centers, expand outward while characters match
- Each expansion is O(n) worst case
- Total: O(n²) time, O(1) space

**5. Implementation**
```python
def longest_palindrome(s: str) -> str:
    if not s:
        return ""
    
    def expand_around_center(left: int, right: int) -> str:
        while left >= 0 and right < len(s) and s[left] == s[right]:
            left -= 1
            right += 1
        return s[left + 1:right]
    
    longest = ""
    for i in range(len(s)):
        # Odd length palindromes
        odd = expand_around_center(i, i)
        if len(odd) > len(longest):
            longest = odd
        
        # Even length palindromes
        even = expand_around_center(i, i + 1)
        if len(even) > len(longest):
            longest = even
    
    return longest
```

**6. Edge case verification**
- Empty string: Returns "" ✓
- Single character "a": Returns "a" ✓
- All same "aaaa": Returns "aaaa" ✓
- No palindrome > 1: "abc" returns "a" ✓
- Multiple same length: Returns first found ✓
```

## Variations

### Numbered Steps
```text
Think through this in exactly 5 steps:
1. [Step 1]
2. [Step 2]
...
```

### Pros/Cons Analysis
```text
Before implementing, list:
- 3 pros of this approach
- 3 cons or risks
- Your recommendation
```

### Rubber Duck Style
```text
Explain your solution to me as if I'm a junior developer. 
Walk through each part and why it's needed.
```

### Decision Matrix
```text
Compare these approaches using a decision matrix:
| Criteria | Approach A | Approach B | Approach C |
|----------|------------|------------|------------|
| Performance | | | |
| Readability | | | |
| Maintainability | | | |
```

## Tips

1. **Be specific about steps**: Vague "think step by step" is less effective than specific steps
2. **Ask for verification**: Include a step to verify the solution
3. **Request trade-off analysis**: Helps surface important considerations
4. **Use for debugging**: "Walk through what happens when input is X"

## Anti-Patterns

- **Don't use for simple tasks**: "Think step by step about how to add two numbers" is overkill
- **Don't skip verification**: Always include a step to check edge cases
- **Don't be too rigid**: Allow AI to add steps if needed

## Related Patterns

- [Rubber Duck Debugging](rubber-duck-debugging.md)
- [Spec-First Implementation](spec-first-implementation.md)
- [Iterative Refinement](iterative-refinement.md)
