# Prompting Guide

> How to write effective prompts and instructions for Devin. Sourced from [Instructing Devin Effectively](https://docs.devin.ai/essential-guidelines/instructing-devin-effectively) and [Coding Agents 101](https://devin.ai/agents101).

## Core Principle

**Be as specific as possible.** Just as you would provide a detailed spec to a coworker, do the same with Devin. The clearer your instructions, the higher the success rate — especially for complex tasks.

---

## Anatomy of a Great Prompt

A well-structured prompt has four elements:

### 1. Helpful Context

Tell Devin *what* you're working on and *why*:

```
"In the payments service repo, I want to add rate limiting to the
checkout endpoint to prevent abuse during flash sales."
```

### 2. Step-by-Step Instructions

Break the work into logical parts:

```
"1. Add a Redis-backed rate limiter middleware
 2. Apply it to the POST /checkout endpoint
 3. Set the limit to 10 requests per minute per user
 4. Return a 429 status with a Retry-After header when exceeded
 5. Add unit tests for the middleware"
```

### 3. Clear Success Criteria

Define what "done" looks like:

```
"The rate limiter should:
 - Pass all existing checkout tests
 - Pass the new rate-limiting tests
 - Not affect other endpoints
 - CI should be green"
```

### 4. References to Existing Patterns

Point to code Devin should follow:

```
"Follow the pattern used in src/middleware/auth.ts for the middleware
structure. Use the same Redis client from src/lib/redis.ts."
```

---

## Best Practices

### Do's

| Practice | Why It Matters |
|----------|---------------|
| **Be opinionated and specific** | Reduces ambiguity and prevents Devin from guessing |
| **Provide file paths and function names** | Saves time exploring the codebase |
| **Include error handling expectations** | Prevents overlooked edge cases |
| **Reference existing code patterns** | Encourages consistency |
| **Specify the testing approach** | Ensures quality validation |
| **Mention libraries to use (or avoid)** | Prevents wrong dependency choices |

### Don'ts

| Anti-Pattern | Problem |
|-------------|---------|
| **Vague instructions** ("make it better") | No clear direction for implementation |
| **Missing context** ("fix the bug") | Which bug? In which file? What's the expected behavior? |
| **Overloading a single prompt** | Too many unrelated tasks reduce quality |
| **Assuming prior knowledge** | Devin doesn't remember previous sessions unless you use Knowledge |
| **Skipping success criteria** | No way to verify the task is complete |

---

## Prompt Templates

### Bug Fix

```
**Bug:** [Description of the bug]
**Reproduction:** [Steps to reproduce]
**Expected behavior:** [What should happen]
**Actual behavior:** [What happens instead]
**Relevant files:** [File paths]
**Fix approach:** [Your suggested approach, if any]
**Tests:** [How to verify the fix]
```

### Feature Implementation

```
**Feature:** [What to build]
**Context:** [Why it's needed, where it fits]
**Approach:**
1. [Step 1]
2. [Step 2]
3. [Step 3]
**Files to modify:** [Paths]
**Patterns to follow:** [Reference existing code]
**Success criteria:** [How to verify]
```

### Refactoring

```
**Refactor:** [What to refactor]
**Current state:** [How it works now]
**Target state:** [How it should work after]
**Constraints:**
- Don't change the public API
- All existing tests must pass
- Follow [pattern/style]
**Files:** [Paths]
```

### Documentation Update

```
**Update docs for:** [Feature/change]
**What changed:** [Summary of the code change]
**Docs to update:** [File paths]
**Style:** [Follow existing doc patterns in X]
```

---

## Defensive Prompting

Anticipate where confusion could arise:

### Ambiguous Terms

**Bad:** "Update the config"
**Good:** "Update the `database` section of `config/production.yaml` to use the new connection pool settings"

### Build/Test Requirements

**Bad:** "Fix the tests"
**Good:** "Fix the failing test in `tests/unit/auth.test.ts`. You'll need to run `npm run build` before `npm test` because the test imports compiled TypeScript."

### Multi-Step Dependencies

**Bad:** "Set up the database and API"
**Good:** "First, run the database migration (`npm run migrate`), then start the API server (`npm run dev`). The API won't start without the migration completing first."

---

## Using Ask Devin for Prompt Generation

Before starting a session, use **Ask Devin** to:

1. **Explore the codebase** — search for relevant files and patterns
2. **Scope the approach** — understand what needs to change
3. **Auto-generate a prompt** — let Ask Devin create a high-context prompt

This produces better prompts because Ask Devin has already analyzed the codebase.

---

## Iteration and Feedback

### Mid-Session Guidance

If Devin's approach drifts, provide course corrections:

```
"Stop. The current approach is adding too much complexity. Instead,
use the existing UserService.validate() method and add the check there.
Don't create a new service."
```

### Teaching Verification

Don't just say "this is wrong." Teach how to verify:

```
"The login test is failing because it's not waiting for the redirect.
To test this manually: run `npm run dev`, go to localhost:3000/login,
enter test@example.com / password123, and verify it redirects to /dashboard."
```

### Save Repeated Feedback as Knowledge

If you find yourself giving the same correction across sessions, save it as a **Knowledge** item so Devin remembers it permanently.

---

## Source

- [Instructing Devin Effectively](https://docs.devin.ai/essential-guidelines/instructing-devin-effectively)
- [Good vs. Bad Instructions](https://docs.devin.ai/essential-guidelines/good-vs-bad-instructions)
- [Prompt Templates Cheat Sheet](https://docs.devin.ai/essential-guidelines/prompt-templates-cheat-sheet)
