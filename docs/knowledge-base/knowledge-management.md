# Knowledge Management

> Devin's knowledge system — creating, organizing, and using persistent knowledge across sessions. Sourced from [Knowledge — Devin Docs](https://docs.devin.ai/product-guides/knowledge).

## What is Knowledge?

Knowledge is a collection of **tips, advice, and instructions** that Devin references across all sessions. Like onboarding a new engineer, it requires an initial investment in knowledge transfer — but once added, Devin automatically recalls relevant knowledge as needed.

Use Knowledge to share:
- Documentation and internal references
- Custom internal library usage
- Common pitfalls and their solutions
- Testing procedures and workflows
- Architecture decisions and patterns

---

## Creating Knowledge

### Via the Devin App

1. Navigate to **Settings & Library > Knowledge**
2. Click **"Add Knowledge"**
3. Fill in:
   - **Trigger Description** — when Devin should recall this (e.g., "When working with the payment service")
   - **Content** — the actual knowledge (a few sentences of relevant information)

### Via Knowledge Suggestions

Devin automatically suggests knowledge based on your chat feedback:
- Edit the suggestion before saving
- Dismiss if not helpful
- Request Devin to regenerate based on your feedback
- Devin can suggest updates to existing items, not just new ones

---

## Key Components

### Trigger Description

The trigger tells Devin *when* to recall the knowledge. Write it as a simple phrase or sentence:

| Good Triggers | Bad Triggers |
|--------------|-------------|
| "When running tests in the payments repo" | "Tests" (too vague) |
| "When creating a new API endpoint" | "API" (too broad) |
| "When deploying to staging" | "Deploy stuff" (unclear) |

### Content

Keep content concise and actionable:

```
When running tests in the payments repo, always use
`npm run test:payments` instead of `npm test`. The global test
command skips the payment-specific database setup. Also, tests
require the `STRIPE_TEST_KEY` secret to be configured.
```

### Macros

Assign a **macro** (short identifier starting with `!`) to any knowledge item:
- Example: `!deploy-checklist`, `!test-payments`, `!auth-setup`
- Reference in prompts by typing the macro name
- Must be unique within your organization
- Can only contain letters, numbers, and hyphens

---

## What Belongs in Knowledge?

### Good Knowledge Items

| Category | Example |
|----------|---------|
| **Repeated prompt fragments** | "Always use the `v2` API client, not `v1`" |
| **Common bugs and fixes** | "If `webpack` fails with ENOMEM, increase Node heap: `NODE_OPTIONS=--max-old-space-size=4096`" |
| **Testing procedures** | "Run `docker-compose up -d db` before running integration tests" |
| **Architecture notes** | "The auth service is in `services/auth` and uses JWT tokens stored in Redis" |
| **Style preferences** | "Use functional components with hooks, not class components" |
| **Tool usage** | "Use `pnpm` instead of `npm` for all package operations" |
| **Environment specifics** | "The staging API is at `api.staging.example.com`, not `staging-api.example.com`" |

### Bad Knowledge Items

| Anti-Pattern | Why |
|-------------|-----|
| Entire README contents | Too broad; Devin already reads READMEs |
| One-time instructions | Use prompts or playbooks instead |
| Frequently changing info | Will become stale and mislead Devin |
| Secrets or credentials | Use the Secrets system, not knowledge |

---

## Organizing Knowledge with Folders

Group related knowledge items into folders for better organization:

```
knowledge/
├── testing/
│   ├── unit-test-patterns
│   ├── integration-test-setup
│   └── e2e-test-commands
├── deployment/
│   ├── staging-checklist
│   └── production-process
├── architecture/
│   ├── service-overview
│   └── database-schema
└── common-issues/
    ├── build-failures
    └── auth-problems
```

---

## Enabling and Disabling Knowledge

Each knowledge item can be individually **enabled or disabled per user**:
- Disabling prevents Devin from retrieving it in your sessions
- The item isn't deleted from the organization
- Useful when a knowledge item is temporarily irrelevant to your work
- Teammates can still use it

---

## Organization and Enterprise Knowledge

### Scopes

| Scope | Visibility | Managed By |
|-------|-----------|------------|
| **User** | Only your sessions | You |
| **Organization** | All users in the org | Org admins |
| **Enterprise** | All orgs in the enterprise | Enterprise admins |

### Promoting Knowledge

Organization-level knowledge can be promoted to enterprise-level, making it available across all organizations.

### Pinning to Repos

Pin knowledge items to specific repositories so they're only recalled when Devin works on those repos. This keeps context tight and avoids irrelevant knowledge surfacing.

---

## Tips and Tricks

1. **Start small** — Add knowledge as you notice repeated corrections, not all at once
2. **Be specific** — "Use `pnpm run test:unit`" is better than "run the tests"
3. **Update regularly** — Remove or update stale knowledge items
4. **Use macros** — For frequently referenced procedures
5. **Pin to repos** — Keep knowledge scoped to relevant repositories
6. **Review suggestions** — Devin's auto-suggestions often capture useful patterns you'd forget to add manually

---

## Source

- [Knowledge — Devin Docs](https://docs.devin.ai/product-guides/knowledge)
- [Knowledge Onboarding](https://docs.devin.ai/onboard-devin/knowledge-onboarding)
