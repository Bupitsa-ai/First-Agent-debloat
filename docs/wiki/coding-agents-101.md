# Coding Agents 101

> A comprehensive guide to working effectively with autonomous coding agents. Sourced from the [Coding Agents 101](https://devin.ai/agents101) guide by Cognition AI.

## Introduction

Coding agents are autonomous software that take initial task descriptions and produce final pull requests with little human intervention. They represent the latest evolution in developer tooling:

| Era | Tool Type | Capability |
|-----|-----------|------------|
| ~2015 | Autocomplete & IntelliSense | Method name suggestions, programmatic refactors |
| ~2021 | Copilots & Tab-Complete | Next few lines of code |
| ~2023 | Generative Chatbots | Assisted development, entire file generation |
| **2025** | **Autonomous Agents** | Initial description to final pull request |

A human paired with an AI assistant achieves more than AI alone, but an autonomous agent's end-to-end capability enables a new level of multi-tasking — **turning every engineer into an engineering manager**.

---

## Prompting Basics

### Say *How*, Not Just *What*

Think of the agent as a junior coding partner. Simple tasks can be described directly, but complex tasks need a clear approach upfront.

**Bad:** "Add unit tests"

**Good:** "Add unit tests for the `OrderService.calculateTotal()` method. Test the following edge cases: empty cart, single item, discount codes, and tax calculation. Mock the `PricingAPI` dependency. Use Jest and follow the existing test patterns in `__tests__/services/`."

### Tell the Agent Where to Start

Even partial direction saves significant time:

**Bad:** "Add Google model support"

**Good:** "Add support for Google models to our code. Look at the latest docs [here](link) and create a new implementation file in the `model-groups` directory. Follow the pattern used in `openai-provider.ts`."

### Practice Defensive Prompting

Anticipate where a new intern would get confused:

```
"Fix the C++ bindings for our search module to pass the new unit tests.
Be careful: you will need to recompile the bindings each time you
change the code before running tests."
```

### Give Access to CI, Tests, Types, and Linters

Agents thrive on feedback loops. The more automated checks available, the better they self-correct:

- **Prefer typed languages** — TypeScript over JavaScript, typed Python over untyped
- **Teach the agent how to run checks** — test commands, lint commands, build commands
- **Ensure packages and access rights** are available
- **Provide browser instructions** if front-end testing is needed

### Leverage Your Expertise

Your codebase familiarity makes you uniquely valuable for:
- Verifying logic and results
- Making architectural decisions
- Providing context the agent can't infer

> Human oversight remains essential. You hold responsibility for final code correctness.

---

## Integrating Agents Into Your Workflow

### Take On New Tasks Immediately

When a teammate asks "could we build X?" — delegate to an agent instead of context-switching. Tag `@Devin` on Slack for bug fixes and minor features.

### Code on the Go

Autonomous agents support mobile access. Address urgent bugs from your phone via Slack or the Devin mobile interface.

### Hand Off Your Chores

Repetitive tasks agents handle well:
- Git bisection for old commits
- Documentation updates for new features
- Dependency version bumps
- Boilerplate generation

### Skip Analysis Paralysis

Can't choose between two approaches? Have the agent implement both. Compare concrete results instead of debating abstractions.

### Set Up Preview Deployments

Configure CI/CD to create preview deployments for every PR. This makes reviewing agent-produced frontend work effortless. Platforms like Vercel make this trivial.

---

## Delegating Larger Tasks

### The Drafting Model

For substantial tasks, use the agent to create an **initial draft PR**, then refine:

| Phase | Agent Does | Human Does |
|-------|------------|------------|
| **Drafting** | Gets started on tasks, creates first-draft solutions | Provides clear instructions and approach |
| **Refining** | Responds to feedback, iterates | Reviews, gives feedback, adds polish |

Expect ~80% time savings on larger tasks, not 100% automation. Multiple feedback cycles are normal.

### Co-Develop a PRD

For complex or vague tasks:
1. Ask discovery questions: "How does our authentication system work?"
2. Have the agent identify relevant code targets
3. Use planning modes (Ask Devin, DeepWiki) to scope before coding
4. Confirm the plan, then let the agent execute

### Set Checkpoints

For multi-part tasks across layers (database, backend, frontend):

```
Plan → Implement chunk → Test → Fix → Checkpoint review → Next chunk
```

Request pauses after each phase. Correct course early to prevent cascading issues.

### Teach It to Verify Its Own Work

Don't just point out issues ("this function isn't working"). Articulate your testing process so the agent can independently verify in the future. Save testing procedures to the agent's knowledge base.

### Increase Test Coverage in AI Hot Spots

Strengthen test coverage in areas the agent modifies frequently. Good tests let you merge agent PRs with confidence.

---

## Automation

### Reusable Prompt Templates (Playbooks)

Create robust, reusable prompts for repetitive scenarios:
- Feature flag removal
- Dependency upgrades
- Adding tests to new feature PRs

An experienced engineer writes the template once; anyone can use it for all similar tasks.

### Intelligent Code Review

Autonomous agents can deliver accurate code review insights, especially if they've indexed your repositories. Maintain a list of common engineering mistakes and have an agent check every new PR.

### Hook Into Incidents and Alerts

Trigger agents automatically in response to events via API or CLI. Combine with MCP integrations for third-party error log ingestion.

> For production debugging, ask the agent to flag suspicious errors rather than fix end-to-end. Humans decide root cause; agents implement the fix.

---

## Customization & Performance

### Environment Setup

Align the agent's environment with your team's:
- Language versions and package dependencies
- Pre-commit hooks and automated checks
- Browser logins and authentication
- Secrets and environment variables via `.envrc` or `.bashrc`

### Build Custom CLI Tools and MCPs

- **MCPs**: Connect to external tools (Datadog, Sentry, databases, Figma, etc.)
- **CLI scripts**: Give agents tools for common workflow parts (e.g., pull Linear ticket info, restart dev environment)

### Add to the Knowledge Base

Codify common mistakes and corrections:
- Overall architecture descriptions
- Testing patterns for different task types
- Important commands and recommended tools
- Boilerplate locations for common operations (e.g., adding a new service route)

---

## Limitations

| Limitation | Mitigation |
|-----------|------------|
| **Limited debugging skills** | Ask for a list of probable root causes, not end-to-end fixes |
| **Poor fine-grained visual reasoning** | Use design systems with reusable components; provide code from Figma |
| **Knowledge cutoffs** | Point explicitly to latest library docs |

---

## Managing Time and Losses

### Cut Losses Early

If the agent is going in circles or ignoring instructions, start fresh rather than sending more messages. The complexity of the task likely exceeds the agent's current capabilities.

### Diversify Experiments

Try a range of task types early. Double down on what works; stop forcing what doesn't.

### Start Fresh When Stuck

Starting over with a new session and all instructions upfront often succeeds faster than trying to correct a struggling session.

---

## Security

| Practice | Details |
|----------|---------|
| **Throwaway email** | For safe testing of sites |
| **Custom IAM roles** | Scoped cloud access for the agent |
| **Dev/staging environment** | Same setup as your team; avoid production access |
| **Readonly API keys** | Where possible; humans run scripts touching external services |

---

## Source

- [Coding Agents 101 — Cognition AI](https://devin.ai/agents101)
- [When to Use Devin](https://docs.devin.ai/essential-guidelines/when-to-use-devin)
