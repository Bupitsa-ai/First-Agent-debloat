# 02 — Coding Agents 101 (Primer)

> Source: <https://devin.ai/agents101>. Product-agnostic; the principles apply to any
> coding agent. This page summarises the parts most relevant to First-Agent.

## Prompting basics (memorise these five)

1. **Say *how* you want things done, not just *what*.** Outline your preferred approach
   up front; it's a junior engineer with unreliable judgement but infinite energy.
   - ❌ "Add unit tests."
   - ✅ "Add unit tests for `parse_tool_call`. Cover: empty input, malformed JSON, unicode, nested calls. Mock the LLM client with `respx`."
2. **Tell the agent where to start.** Name the repo, file, function, or doc link.
   - ✅ "Add Google models to `src/providers/`. Look at the latest docs at <link>."
3. **Practice defensive prompting.** Imagine giving the same prompt to a new intern —
   where would confusion arise? Pre-answer it.
4. **Give access to CI, tests, types, and linters.** Feedback loops are where agents
   become magic. Prefer typed Python / TS. Teach Devin how to run the checks.
5. **Leverage your expertise.** You still own correctness. Verify results; don't merge
   what you can't defend.

## Using agents in your workflow

| Pattern | Example for First-Agent |
|---|---|
| **Take on new tasks immediately** | "Could we try a ReAct-style loop?" → spawn a session instead of interrupting your current flow. |
| **Code on the go** | Slack-tag Devin from mobile to kick off a research writeup. |
| **Hand off your chores** | Updating README / changelog / doc strings after a refactor. |
| **Skip analysis paralysis** | "Implement both LangGraph and a hand-rolled state machine for the orchestrator; draft PRs for comparison." |
| **Preview deployments** | Hook up Vercel / Netlify for any UI demo we build. |

## Delegating larger tickets (1–6 hr tasks)

The highest ROI comes here. Think of yourself as the *architect*, Devin as the
*drafter*. Expect multiple feedback cycles on large tasks. Realistic target: **~80%
time saved, not 100% automation.**

### Drafting vs refining

| Domain | Drafting | Refining |
|---|---|---|
| Journalism | Journalist collects info, writes first draft | Editor fact-checks, polishes, publishes |
| Restaurant | Line cooks prep ingredients and preliminary dishes | Sous chef seasons and finishes before serving |
| **Coding** | **Agent drafts PR from plan** | **Human reviews, gives feedback, adds polish before merge** |

### Co-develop a PRD

For vague/large tasks, build the plan *with* the agent first. Ask it to:

1. Read the relevant code and docs.
2. Produce a written plan (files to change, risks, test approach).
3. Iterate on that plan with you before writing any code.

Then the implementation session starts from an already-agreed-upon plan.

## Useful habits we've adopted for First-Agent

- Keep prompts in [`knowledge/prompts/`](../knowledge/prompts/) so they are versioned.
- Log every non-obvious decision as an ADR in [`knowledge/adr/`](../knowledge/adr/).
- Treat Devin's PR as a *draft* by default; only mark ready-for-review after human polish.
- Never merge without running the full test suite locally at least once, even if CI is green.
