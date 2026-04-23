# Building Your Agent

> Architecture and research guide for the First-Agent LLM project. How to leverage Devin's tools and patterns while designing your own autonomous agent.

## Project Context

**First-Agent** is a research-stage project focused on creating a new LLM agent. This guide captures architectural patterns, design decisions, and lessons learned from the Devin.ai ecosystem that are directly applicable to building autonomous coding agents.

---

## Agent Architecture Layers

Based on the Devin model, an LLM agent typically has three layers:

### 1. Instruction Layer

The system prompts and behavioral guidelines that shape agent behavior:

```
┌─────────────────────────────────────┐
│         Instruction Layer            │
│                                      │
│  System Prompt ← defines persona,    │
│                  capabilities,       │
│                  constraints         │
│                                      │
│  Knowledge ← persistent tips,        │
│               corrections,           │
│               project context        │
│                                      │
│  Skills ← step-by-step procedures    │
│            committed to repos        │
│                                      │
│  Playbooks ← reusable task templates │
└─────────────────────────────────────┘
```

**Key design decisions:**
- How does the agent receive and prioritize instructions?
- How is persistent knowledge stored and recalled?
- How are procedures (skills) triggered and followed?

### 2. Execution Layer

The runtime environment where the agent actually does work:

```
┌─────────────────────────────────────┐
│          Execution Layer             │
│                                      │
│  Shell ← command execution,          │
│           build tools, tests         │
│                                      │
│  IDE ← file reading, writing,        │
│         code navigation              │
│                                      │
│  Browser ← web interaction,          │
│             testing, research        │
│                                      │
│  Desktop ← GUI interaction,          │
│             visual testing           │
└─────────────────────────────────────┘
```

**Key design decisions:**
- What tools does the agent have access to?
- How does the agent observe and react to tool outputs?
- How are errors and failures handled?
- What are the agent's resource limits?

### 3. Integration Layer

External connections that extend the agent's capabilities:

```
┌─────────────────────────────────────┐
│         Integration Layer            │
│                                      │
│  MCP ← Model Context Protocol        │
│         (external tools/data)        │
│                                      │
│  Git ← version control, PRs,         │
│         code review                  │
│                                      │
│  CI/CD ← build pipelines,            │
│           test runners               │
│                                      │
│  Messaging ← Slack, Teams,           │
│               notifications          │
└─────────────────────────────────────┘
```

**Key design decisions:**
- Which external services does the agent connect to?
- How is authentication managed?
- How are rate limits and quotas handled?

---

## Core Design Patterns

### Pattern 1: Feedback Loop

The most important pattern for agent success. Agents thrive when they can:
1. Take an action
2. Observe the result
3. Correct course if needed

```
Action → Observation → Reflection → Next Action
  │                                      │
  └──────────── Feedback Loop ───────────┘
```

**Implementation:**
- Run tests after code changes (immediate feedback)
- Check linter output (code quality feedback)
- Verify builds succeed (compilation feedback)
- Test in browser (visual/functional feedback)

### Pattern 2: Planning Before Execution

For complex tasks, plan first:

```
1. Analyze the task requirements
2. Explore the codebase for relevant files
3. Create a step-by-step plan
4. Execute each step
5. Verify after each step
6. Deliver the result
```

### Pattern 3: Escalation

Know when to ask for help:

```
if task_is_clear:
    execute()
elif task_is_ambiguous:
    ask_clarifying_questions()
    then_execute()
elif task_exceeds_capability:
    report_blockers()
    suggest_alternatives()
```

### Pattern 4: Knowledge Accumulation

Learn from each session:

```
Session 1: Discover that tests need Docker
  → Save Knowledge: "Run docker-compose up before tests"

Session 2: Knowledge is automatically recalled
  → Docker starts before tests
  → Task succeeds on first try
```

---

## Research Considerations

### LLM Selection

| Factor | Consideration |
|--------|--------------|
| **Context window** | Larger windows allow more code context |
| **Code generation quality** | Test on your specific language/framework |
| **Tool use capability** | The model must reliably call tools in the right format |
| **Instruction following** | Ability to follow complex, multi-step procedures |
| **Cost vs. quality** | Balance per-token cost with task success rate |

### Tool Design

Design tools that:
- **Return structured output** — Make it easy for the LLM to parse results
- **Include error information** — Help the agent self-correct
- **Have clear boundaries** — Each tool does one thing well
- **Are composable** — Tools can be combined for complex operations

### Memory Architecture

| Type | Purpose | Example |
|------|---------|---------|
| **Session memory** | Context within a single task | Current file contents, previous commands |
| **Persistent knowledge** | Facts that apply across sessions | "Use pnpm, not npm" |
| **Procedural memory** | Step-by-step workflows | SKILL.md files |
| **Episodic memory** | Past session outcomes | Session Insights data |

### Error Recovery

Design your agent to handle:
1. **Tool failures** — Retry with backoff, try alternative approaches
2. **LLM errors** — Malformed output, hallucinated function calls
3. **Environment issues** — Missing dependencies, network failures
4. **Task ambiguity** — Ask for clarification rather than guessing

---

## Lessons from the Devin Ecosystem

### What Works

1. **Typed languages** — TypeScript > JavaScript, typed Python > untyped
2. **Strong test suites** — Agents self-correct better with automated tests
3. **Clear project structure** — AGENTS.md and Skills reduce exploration time
4. **Focused tasks** — Smaller, well-defined tasks succeed more often
5. **Preview deployments** — Instant visual verification of changes

### What Doesn't Work (Yet)

1. **Complex debugging** — Agents struggle with multi-step debugging
2. **Fine-grained visual reasoning** — Pixel-perfect design matching is unreliable
3. **Unbounded exploration** — Tasks without clear end criteria drift
4. **Production access** — Keep agents in dev/staging environments

### The 80% Rule

Expect ~80% time savings on larger tasks, not 100% automation. Human oversight remains essential for:
- Architectural decisions
- Security review
- Final quality assurance
- Edge case handling

---

## Next Steps for First-Agent

### Research Phase

1. **Define the agent's scope** — What types of tasks will it handle?
2. **Choose the LLM** — Evaluate models on your target task types
3. **Design the tool set** — What tools does the agent need?
4. **Define the knowledge system** — How will the agent learn and remember?

### Prototype Phase

1. **Build a minimal execution loop** — Action → Observation → Next Action
2. **Implement core tools** — Shell, file system, git at minimum
3. **Add a planning step** — Have the agent plan before executing
4. **Test on real tasks** — Use tasks from your own workflow

### Iteration Phase

1. **Analyze failures** — Categorize why tasks fail
2. **Improve prompts** — Refine system prompts based on failure modes
3. **Expand tools** — Add tools that address common failure points
4. **Build the knowledge system** — Persistent learning across sessions

---

## Source

- [Coding Agents 101](https://devin.ai/agents101)
- [Devin Overview](../wiki/devin-overview.md)
- [Skills Reference](../knowledge-base/skills-reference.md)
- [Knowledge Management](../knowledge-base/knowledge-management.md)
