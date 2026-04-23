# Automation & Workflows

> Scheduled sessions, CI/CD integration, event-driven automation, and workflow patterns with Devin.

## Overview

Devin supports three levels of automation:

| Level | Method | Trigger |
|-------|--------|---------|
| **Manual** | Chat, Slack, Teams | Human starts a session |
| **Scheduled** | Scheduled Sessions | Time-based (daily, weekly, cron) |
| **Event-Driven** | Devin API, Webhooks | External events (CI failures, alerts, PRs) |

---

## Scheduled Sessions

### What Are Scheduled Sessions?

Automated Devin sessions that run on a schedule — daily, weekly, or custom cron expressions. Perfect for recurring maintenance tasks.

### Common Use Cases

| Task | Schedule | Example Prompt |
|------|----------|---------------|
| **Dependency updates** | Weekly | "Check for outdated npm packages and create a PR upgrading minor/patch versions" |
| **Error triage** | Daily | "Check Sentry for new errors in the last 24 hours. Categorize by severity and create Linear tickets for critical ones" |
| **Report generation** | Weekly | "Generate a weekly summary of merged PRs and their impact" |
| **Security scanning** | Daily | "Run `npm audit` and create tickets for any new high/critical vulnerabilities" |
| **Documentation sync** | Weekly | "Check if any API endpoints have changed since last week and update the docs accordingly" |
| **Test coverage** | Weekly | "Run the test suite, generate a coverage report, and flag any files below 80% coverage" |

### Setting Up a Scheduled Session

1. Navigate to **Settings & Library > Scheduled Sessions**
2. Click **"Create Schedule"**
3. Configure:
   - **Name**: Descriptive name (e.g., "Weekly dependency updates")
   - **Schedule**: Cron expression or preset (daily, weekly)
   - **Prompt**: The task instructions
   - **Repository**: Which repo to work on
   - **Playbook** (optional): Use a playbook for complex tasks

### Cron Expression Examples

| Expression | Meaning |
|-----------|---------|
| `0 9 * * 1` | Every Monday at 9:00 AM |
| `0 0 * * *` | Every day at midnight |
| `0 9 * * 1-5` | Every weekday at 9:00 AM |
| `0 0 1 * *` | First day of every month |

---

## Event-Driven Automation

### Devin API

Use the Devin API to programmatically create sessions in response to events:

```python
import requests

def trigger_devin_session(task_description, repo):
    response = requests.post(
        "https://api.devin.ai/v1/sessions",
        headers={"Authorization": f"Bearer {DEVIN_API_KEY}"},
        json={
            "prompt": task_description,
            "repo": repo
        }
    )
    return response.json()
```

### Common Event-Driven Patterns

#### On CI Failure

```python
# Webhook handler for CI failure
def on_ci_failure(event):
    trigger_devin_session(
        f"CI failed on branch {event['branch']}. "
        f"The failing job is {event['job_name']}. "
        f"Investigate the failure and create a fix PR.",
        repo=event['repo']
    )
```

#### On New Sentry Error

```python
# Triggered by Sentry webhook
def on_sentry_alert(event):
    trigger_devin_session(
        f"A new error was detected in production: {event['title']}. "
        f"Stack trace: {event['stacktrace']}. "
        f"Investigate the root cause and suggest a fix.",
        repo=event['repo']
    )
```

#### On PR Creation (Auto-Review)

Enable **Devin Review** to automatically review every new PR:
- Navigate to **Settings > Devin Review**
- Enable for your repositories
- Configure auto-fix to let Devin respond to its own review comments

---

## Workflow Patterns

### Pattern 1: Reusable Prompt Templates (Playbooks)

Create robust playbooks for repetitive scenarios:

```
Feature Flag Removal:
1. Find all references to the flag
2. Remove conditional logic
3. Clean up dead code paths
4. Update tests
5. Create PR

Dependency Upgrade:
1. Check for outdated packages
2. Upgrade minor/patch versions
3. Run tests
4. Fix any breaking changes
5. Create PR
```

### Pattern 2: Managed Devins (Parallel Delegation)

Ask Devin to delegate sub-tasks to child sessions:

```
"I need to update the error handling in all our API services.
Please create managed Devin sessions for each service:
1. Session for user-service: Update error handling
2. Session for payment-service: Update error handling
3. Session for notification-service: Update error handling
Each should follow the error-handling-v2 pattern in the shared library."
```

### Pattern 3: Intelligent Code Review Pipeline

```
New PR Created
    │
    ▼
Devin Review (automatic)
    │
    ├── Issues found → Auto-Fix creates commits
    │                      │
    │                      ▼
    │               Re-review (automatic)
    │
    └── No issues → Ready for human review
```

### Pattern 4: Incident Response

```
Alert Triggered (Sentry/Datadog)
    │
    ▼
Devin Session (API-triggered)
    │
    ├── Investigate logs and recent changes
    │
    ├── Identify probable root causes
    │
    ├── Flag suspicious commits/deploys
    │
    └── Create summary ticket with findings
         │
         ▼
    Human decides on fix approach
         │
         ▼
    Devin implements the fix
```

---

## Slack & Teams Integration

### Starting Sessions from Chat

Tag `@Devin` in a Slack or Teams conversation:

```
@Devin Can you look into why the checkout flow is throwing 500 errors?
Check the payment-service logs in Datadog for the last hour.
```

Devin responds in-thread with updates and delivers the result.

### Best Practices

- Use threads to keep conversations organized
- Provide context directly in the message
- Tag relevant channels for visibility
- Use Devin for quick investigations and fixes during team discussions

---

## CI/CD Integration

### Preview Deployments

Set up preview deployments for every PR:

| Platform | Setup |
|----------|-------|
| **Vercel** | Connect repo → auto-deploys on PR |
| **Netlify** | Connect repo → deploy previews enabled |
| **Cloudflare Pages** | Connect repo → preview URLs per PR |

Preview URLs let you visually verify Devin's frontend changes without running locally.

### CI Pipeline Tips

1. **Fast feedback** — Keep CI runs under 10 minutes for quick iteration
2. **Clear error messages** — Help Devin understand what failed
3. **Separate stages** — Lint, test, build as distinct steps
4. **Required checks** — Enforce CI passing before merge

---

## Building Your Automation Stack

### Starter Setup

```
1. Enable Devin Review (automatic PR review)
2. Enable Auto-Fix (automatic iteration on review comments)
3. Set up preview deployments (visual verification)
4. Add a weekly scheduled session for dependency updates
```

### Intermediate Setup

```
5. Connect Slack/Teams for conversational sessions
6. Add MCP integrations (Sentry, Datadog, etc.)
7. Create playbooks for your most common tasks
8. Set up daily error triage sessions
```

### Advanced Setup

```
9.  Use the Devin API for event-driven automation
10. Set up managed Devins for parallel task delegation
11. Build custom CLI tools and MCPs for your specific workflows
12. Create a custom code review checklist enforced by Devin
```

---

## Source

- [Scheduled Sessions — Devin Docs](https://docs.devin.ai/product-guides/scheduled-sessions)
- [Devin API](https://docs.devin.ai/api-reference/overview)
- [Devin Review](https://docs.devin.ai/work-with-devin/devin-review)
- [Coding Agents 101 — Automation](https://devin.ai/agents101#automating-workflows)
