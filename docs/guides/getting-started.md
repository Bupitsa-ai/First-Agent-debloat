# Getting Started with Devin

> First session setup, environment configuration, and onboarding checklist.

## Prerequisites

Before your first Devin session, ensure you have:

- [ ] A Devin account at [app.devin.ai](https://app.devin.ai)
- [ ] At least one repository connected (GitHub, GitLab, or Bitbucket)
- [ ] Repository indexed (Settings > Repositories > Index)

---

## Your First Session

### 1. Start a Session

Open the Devin app and type your task in the chat. Start with something simple:

```
"In the First-Agent repo, add a CONTRIBUTING.md file that describes
how to contribute to this project. Follow the style of the existing README.md."
```

### 2. Watch Devin Work

Use the **Progress tab** to monitor:
- **Shell**: See commands Devin runs
- **IDE**: Watch code being written
- **Browser**: See any web interactions

### 3. Review the PR

Devin creates a pull request when done. Review it, leave comments, and merge.

---

## Environment Configuration

### What is Environment Config?

Environment configuration defines how Devin's machine is set up at the start of every session. This includes:
- Language versions (Node.js, Python, Rust, etc.)
- Package manager preferences
- Environment variables
- Build tools and dependencies

### Setting Up

Navigate to **Settings > Environment** in the Devin app.

#### Repository-Level Config

Runs at the start of sessions working on a specific repo:

```yaml
setup:
  - nvm install 18
  - nvm use 18
  - npm install
```

#### Organization-Level Config

Runs before every session, across all repos:

```yaml
setup:
  - nvm install 18
  - nvm use 18
```

---

## Onboarding Checklist

### Phase 1: Basic Setup

- [ ] **Connect repositories** — Link your GitHub/GitLab/Bitbucket account
- [ ] **Index repos** — Let Devin analyze your codebase
- [ ] **Configure environment** — Set up language versions, dependencies
- [ ] **Add secrets** — API keys, database credentials (Settings > Secrets)

### Phase 2: Knowledge Transfer

- [ ] **Add AGENTS.md** — Create an `AGENTS.md` file in your repo root with setup commands, code style, testing guidelines, and project structure
- [ ] **Add Knowledge items** — Common pitfalls, testing procedures, architecture notes
- [ ] **Create Skills** — Commit `SKILL.md` files for repeatable procedures (testing, deploying)

### Phase 3: Integration

- [ ] **Connect Slack or Teams** — Tag @Devin in conversations to start sessions
- [ ] **Enable Devin Review** — Automated PR review with auto-fix
- [ ] **Set up MCP integrations** — Connect Datadog, Sentry, Figma, databases, etc.
- [ ] **Create Playbooks** — Reusable prompt templates for repeated tasks

### Phase 4: Automation

- [ ] **Scheduled Sessions** — Daily/weekly tasks (dependency updates, error triage)
- [ ] **CI/CD integration** — Preview deployments for every PR
- [ ] **Managed Devins** — Delegate sub-tasks to parallel sessions

---

## AGENTS.md

The simplest way to give Devin repo-specific context. Create an `AGENTS.md` file in your project root:

```markdown
# AGENTS.md

## Setup Commands
- Install dependencies: `npm install`
- Start development server: `npm run dev`
- Run tests: `npm test`
- Build for production: `npm run build`

## Code Style
- Use TypeScript strict mode
- Prefer functional components in React
- Follow conventional commit format

## Testing Guidelines
- Write unit tests for all new functions
- Use Jest for testing framework
- Run tests before committing

## Project Structure
- `/src` — Main application code
- `/tests` — Test files
- `/docs` — Documentation
```

Devin reads this file before starting any work in the repo.

---

## Secrets Management

### Adding Secrets

Navigate to **Settings > Secrets** to add:
- API keys
- Database connection strings
- Service credentials
- Environment-specific variables

### Scopes

| Scope | Visibility |
|-------|-----------|
| **Session** | Current session only |
| **User** | All your sessions |
| **Organization** | All users in the org |
| **Repository** | All sessions in a specific repo |

### Best Practices

- Use descriptive names: `STRIPE_TEST_API_KEY`, not `KEY`
- Scope secrets as narrowly as possible
- Use readonly credentials where possible
- Create dedicated service accounts for Devin

---

## Repository Indexing

### What Indexing Does

When you index a repository, Devin:
1. Analyzes the codebase structure
2. Understands file relationships and dependencies
3. Builds a searchable index for Ask Devin
4. Generates DeepWiki documentation

### How to Index

1. Go to **Settings > Repositories**
2. Select the repo
3. Click **"Index"**
4. Wait for indexing to complete

### Re-Indexing

Re-index after major structural changes to keep Devin's understanding current.

---

## VPN Configuration

If your repos or services require VPN access:

1. Provide an OpenVPN configuration file as a secret (`VPN_CONFIG`)
2. Add VPN startup to your environment config:
   ```bash
   echo "$VPN_CONFIG" | sudo tee /etc/openvpn/client/cognition.conf > /dev/null
   sudo systemctl daemon-reload
   sudo systemctl enable --now openvpn-client@cognition
   ```

---

## Next Steps

1. **Read the [Prompting Guide](../knowledge-base/prompting-guide.md)** — Learn how to write effective instructions
2. **Explore [Best Practices](best-practices.md)** — Task evaluation and optimization
3. **Set up [Skills](../knowledge-base/skills-reference.md)** — Define reusable procedures
4. **Browse [MCP Integrations](../wiki/mcp-integrations.md)** — Connect external tools

---

## Source

- [Your First Session — Devin Docs](https://docs.devin.ai/get-started/first-run)
- [AGENTS.md — Devin Docs](https://docs.devin.ai/onboard-devin/agents-md)
- [Knowledge Onboarding](https://docs.devin.ai/onboard-devin/knowledge-onboarding)
