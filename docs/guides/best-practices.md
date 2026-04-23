# Best Practices

> Task evaluation, pre/post checklists, and optimization strategies for working with Devin.

## Evaluating Tasks for Devin

Before assigning a task, ask yourself three questions:

### 1. Can I Describe Clear Success Criteria?

Tasks with verifiable outcomes yield the best results:
- **Test suites** that must pass
- **CI checks** that must be green
- **Specific behavior** that can be observed
- **Code patterns** that must be followed

### 2. Is There Enough Context?

Provide:
- Relevant file paths and function names
- Existing patterns to follow
- Documentation or API references
- Examples of desired input/output

### 3. Would Breaking This Down Help?

For large projects, split into focused sessions:
- Each session handles one logical unit
- Sessions can run in parallel with Managed Devins
- Keep sessions at XS, S, or M size (measured by Session Insights)

> **Rule of thumb:** If a task takes you 3 hours or less, Devin can likely do it. For longer tasks, break them down.

---

## Pre-Task Checklist

Run through this before starting a Devin session:

### Task Definition

- [ ] Clear start and end point defined
- [ ] Explicit success criteria (passing tests, CI green, matching pattern)
- [ ] Scope is appropriate (not too large for a single session)

### Available Context

- [ ] Examples or patterns for Devin to follow identified
- [ ] Prototypes, partial code, or existing patterns referenced
- [ ] Links, filenames, or design files included
- [ ] Relevant MCP integrations connected

### Success Validation

- [ ] Test suite available for verification
- [ ] Lint/type-check commands documented
- [ ] Browser testing instructions provided (if frontend)
- [ ] Devin Review enabled for automatic PR review

### Review Effort

- [ ] Auto-Fix enabled for automated iteration
- [ ] CI pipeline configured to validate changes
- [ ] Preview deployments set up (if frontend)

---

## Post-Task Review

### Monitor Session Trajectory

Use **Session Insights** to:
- Investigate the session timeline
- Identify actionable feedback for future sessions
- Check if Devin hit usage limits (task may be too complex)
- Verify dev environment worked correctly

### Learning from Mistakes

When Devin makes mistakes, turn them into improvements:

| What Happened | Action to Take |
|--------------|----------------|
| Devin used the wrong library | Add Knowledge: "Use library X, not Y" |
| Devin couldn't find a file | Add to prompt: specify file paths |
| Devin skipped testing | Add a Skill for testing procedures |
| Devin's approach was wrong | Create a Playbook with the correct approach |
| Environment setup failed | Update environment configuration |

### Use Session Insights Prompts

Session Insights generates improved prompts based on what happened. Use these as starting points for similar future tasks.

---

## Optimization Strategies

### Run Multiple Sessions in Parallel

Carve out independent tasks and run simultaneously:

```
Session 1: "Add pagination to the /users endpoint"
Session 2: "Refactor the auth middleware to support API keys"
Session 3: "Update documentation for the new billing feature"
```

### Use Ask Devin First

Before implementation sessions:
1. Use Ask Devin to explore the codebase
2. Scope the approach collaboratively
3. Let Ask Devin auto-generate a high-context prompt
4. Start the implementation session with the generated prompt

### Leverage the Drafting Model

For complex tasks:

| Phase | Who | What |
|-------|-----|------|
| **Plan** | You + Ask Devin | Scope the approach, identify files |
| **Draft** | Devin | Implement the first version |
| **Review** | You | Check the PR, leave feedback |
| **Refine** | Devin (Auto-Fix) | Address feedback, fix CI |
| **Merge** | You | Final approval |

### Set Checkpoints for Large Tasks

For multi-layer features (database + backend + frontend):

```
Plan → Implement DB changes → Review → 
Implement backend → Review → 
Implement frontend → Review → 
Integration testing → Merge
```

---

## Common Pitfalls and Solutions

| Pitfall | Solution |
|---------|----------|
| **Prompt too vague** | Add file paths, function names, and specific approach |
| **Task too large** | Break into 2-3 focused sessions |
| **No feedback loops** | Ensure tests, linting, and CI are configured |
| **Repeating the same corrections** | Save as Knowledge items |
| **Session going in circles** | Start fresh with clearer instructions |
| **Wrong dependencies installed** | Specify exact packages in the prompt |
| **Environment setup failing** | Update environment configuration |

---

## Cost Management

### Minimize Wasted Tokens

1. **Cut losses early** — If Devin is going in circles, start a new session
2. **Diversify experiments** — Try different task types to find what works best
3. **Start fresh when stuck** — A new session with clear instructions often succeeds faster than correcting a struggling one
4. **Use the right tool** — Knowledge for tips, Skills for procedures, Playbooks for complex tasks

### Maximize ROI

1. **Target the highest-value tasks** — Medium complexity tasks (1-6 hours) give the best return
2. **Invest in onboarding** — Knowledge, Skills, and environment config pay dividends across all future sessions
3. **Enable automation** — Devin Review, Auto-Fix, and Scheduled Sessions reduce ongoing human effort

---

## Source

- [When to Use Devin](https://docs.devin.ai/essential-guidelines/when-to-use-devin)
- [Session Insights](https://docs.devin.ai/product-guides/session-insights)
- [Coding Agents 101](https://devin.ai/agents101)
