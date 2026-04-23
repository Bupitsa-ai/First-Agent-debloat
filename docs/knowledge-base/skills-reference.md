# Skills Reference

> SKILL.md files — reusable procedures committed to your repos that Devin follows consistently. Sourced from [Skills — Devin Docs](https://docs.devin.ai/product-guides/skills).

## What are Skills?

Skills are `SKILL.md` files committed to your repositories that teach Devin **reusable procedures**. Any repeatable workflow can become a skill:

- Testing your app before opening a PR
- Deploying to an environment
- Investigating a part of the codebase
- Scaffolding a new service
- Running a data migration

Skills follow the open **Agent Skills standard**, so the same skill files work across multiple AI coding tools.

---

## File Location

Place skill files at:

```
.agents/skills/<skill-name>/SKILL.md
```

Devin automatically discovers them across all connected repositories.

### Alternative Locations

Skills are also discovered at:
- `.devin/skills/<skill-name>/SKILL.md`
- `.cursor/skills/<skill-name>/SKILL.md`

---

## Why Skills Matter

| Without Skills | With Skills |
|---------------|-------------|
| Devin figures out workflows from scratch every session | Devin follows a defined procedure every time |
| Inconsistent results across sessions | Reliable, repeatable outcomes |
| You repeat the same instructions | Define once, use forever |

Skills are ideal when a workflow:
- **Should be done the same way every time** — testing checklists, deployment steps
- **Requires repo-specific knowledge** — which services to start, what ports to use
- **Benefits from dynamic context** — pulling in git diffs, branch names, or environment info

---

## Skill File Format

### Basic Structure

```markdown
---
name: test-before-pr
description: Run the local dev server and verify pages before opening any PR
  that touches frontend code
trigger:
  - before creating a pull request that modifies files in src/
---

# Test Before PR

## Steps

1. Install dependencies: `npm install`
2. Start the dev server: `npm run dev`
3. Wait for the server to be ready on port 3000
4. Open Chrome and navigate to http://localhost:3000
5. Verify the home page loads without errors
6. Check the browser console for any JavaScript errors
7. Take a screenshot of the page
8. Stop the dev server
```

### Frontmatter Fields

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Unique identifier for the skill |
| `description` | Yes | What the skill does (used for matching) |
| `trigger` | No | When Devin should automatically invoke this skill |

---

## Examples

### Testing Before PR

```markdown
---
name: test-nextjs-app
description: Test the Next.js app before opening a PR
trigger:
  - before creating a pull request
---

# Test Next.js App

## Steps

1. Run `npm install` to ensure dependencies are up to date
2. Run `npm run build` to verify the build succeeds
3. Run `npm run test` to execute the test suite
4. Start the dev server with `npm run dev`
5. Open http://localhost:3000 in the browser
6. Navigate to key pages: /, /dashboard, /settings
7. Take a screenshot of each page
8. Verify no console errors
9. Stop the dev server
```

### Deploying to Staging

```markdown
---
name: deploy-staging
description: Deploy the application to the staging environment
trigger:
  - when asked to deploy to staging
---

# Deploy to Staging

## Prerequisites
- Ensure all tests pass
- Ensure the branch is up to date with main

## Steps

1. Run `npm run build`
2. Run `npm run test`
3. Run `./scripts/deploy.sh staging`
4. Wait for deployment to complete
5. Verify the staging URL responds: https://staging.example.com
6. Run smoke tests: `npm run test:smoke -- --env=staging`
```

### Investigating a Codebase Area

```markdown
---
name: investigate-auth
description: Investigate the authentication system
trigger:
  - when asked about authentication or auth
---

# Investigate Auth System

## Key Files
- `src/services/auth/` — Main auth service
- `src/middleware/auth.ts` — Auth middleware
- `src/lib/jwt.ts` — JWT token handling
- `config/auth.yaml` — Auth configuration

## Steps

1. Read `src/services/auth/index.ts` for the service entry point
2. Check `src/middleware/auth.ts` for how requests are authenticated
3. Review `src/lib/jwt.ts` for token generation and validation
4. Check `config/auth.yaml` for current configuration
5. Summarize the auth flow and any potential issues
```

---

## Dynamic Content

Skills support dynamic content that's resolved at invocation time:

| Variable | Value |
|----------|-------|
| `{{branch}}` | Current git branch name |
| `{{diff}}` | Git diff of current changes |
| `{{env.VAR_NAME}}` | Environment variable value |

Example:
```markdown
## Steps
1. Review the changes on branch `{{branch}}`
2. Check the diff: {{diff}}
```

---

## How Devin Uses Skills

### Automatic Invocation

Devin matches the current task against skill `description` and `trigger` fields. If there's a match, Devin activates the skill and follows it step by step.

### Manual Invocation

Mention a skill in your prompt:
```
"Use the test-before-pr skill before creating the PR"
```

### One Active Skill at a Time

Devin can only use one skill per session. The most relevant skill is selected based on the task description.

---

## Devin Suggests Skills Automatically

After testing your app or learning something new during a session, Devin suggests creating or updating skills. You'll see:
- A summary of what was learned
- The proposed `SKILL.md` contents
- A **"Create PR"** button to commit the skill

Over time, Devin builds a library of skills about how to run, test, and deploy your application.

---

## Skills vs. Playbooks

| Aspect | Skills | Playbooks |
|--------|--------|-----------|
| **Location** | Committed to the repo (`.agents/skills/`) | Stored in the Devin app |
| **Scope** | Repo-specific procedures | Org-wide reusable prompts |
| **Trigger** | Automatic (pattern matching) or manual | Manual (user selects) |
| **Content** | Step-by-step procedures | Task descriptions with context |
| **Versioning** | Git-versioned with the code | Managed in the Devin UI |
| **Sharing** | Shared via the repo | Shared within the org |

**Use Skills when:** The procedure is tied to a specific repo and should evolve with the code.

**Use Playbooks when:** The task is org-wide and not tied to a specific repo.

---

## Source

- [Skills — Devin Docs](https://docs.devin.ai/product-guides/skills)
- [Agent Skills Standard](https://github.com/anthropics/agent-skills)
