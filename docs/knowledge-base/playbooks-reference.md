# Playbooks Reference

> Reusable prompt templates for repeated tasks across your organization. Sourced from [Creating Playbooks — Devin Docs](https://docs.devin.ai/product-guides/creating-playbooks).

## What are Playbooks?

A playbook is a **custom system prompt for a repeated task**. Think of it as a reusable, shareable template that ensures consistent results across different Devin sessions and team members.

**Example use case:** If you frequently have Devin integrate the same third-party library across different parts of your app, a playbook captures the exact approach, constraints, and verification steps — so anyone on the team can use it.

---

## When to Use Playbooks

| Use Playbooks When | Use Knowledge Instead When |
|-------------------|--------------------------|
| You'll reuse the prompt across multiple sessions | It's a general tip or guideline |
| You find yourself repeating the same reminders | It's a one-liner correction |
| The task is relevant to other team members | It's personal preference |
| The task involves a complex multi-step procedure | It's a simple fact about the codebase |

> **Read the [Knowledge docs](knowledge-management.md) first** to understand which method fits your needs.

---

## Playbook Structure

A well-written playbook has these sections:

### 1. Procedure

The complete scope of the task, from setup to delivery:

```markdown
## Procedure

1. **Setup**: Clone the repo and install dependencies
2. **Implementation**: Create the new API endpoint following the existing pattern
3. **Testing**: Run the test suite and add new tests for the endpoint
4. **Delivery**: Create a PR with a clear description
```

### 2. Specifications (Postconditions)

What should be true after Devin finishes:

```markdown
## Specifications

- The endpoint responds to GET /api/v2/users with a paginated JSON response
- Pagination defaults to 20 items per page
- The response includes a `total` count field
- All existing tests still pass
- New tests cover: empty list, single page, multi-page, invalid page number
```

### 3. Advice and Pointers

Tips to correct Devin's assumptions:

```markdown
## Advice

- Use the `v2` router in `src/routes/v2/`, not the deprecated `v1` router
- The database client is already configured in `src/lib/db.ts` — don't create a new one
- Follow the pagination pattern in `GET /api/v2/products` (see `src/routes/v2/products.ts`)
```

### 4. Forbidden Actions

Guardrails to prevent common mistakes:

```markdown
## Forbidden Actions

- Do NOT modify the database schema
- Do NOT change existing API response formats
- Do NOT install new dependencies without checking existing ones first
- Do NOT skip the TypeScript type-check step
```

### 5. Required from User

Input the user must provide each time:

```markdown
## Required from User

- The specific resource name (e.g., "users", "orders", "products")
- Any custom filtering requirements
- The database table name to query
```

---

## Creating a Playbook

### In the Devin App

1. Navigate to **Settings & Library > Playbooks**
2. Click **"Create Playbook"**
3. Write the playbook using the structure above
4. Test it with a Devin session
5. Iterate based on results

### From a Successful Session

After a successful Devin session, you can save the prompt as a playbook for future reuse. This captures the exact instructions that worked.

---

## Example Playbook

```markdown
# Integrate Third-Party Analytics SDK

## Procedure

1. Install the analytics SDK: `npm install @analytics/sdk`
2. Create a wrapper module at `src/lib/analytics.ts` that:
   - Initializes the SDK with the project's API key (from env var `ANALYTICS_KEY`)
   - Exports typed helper functions for common events
   - Handles initialization failure gracefully
3. Add analytics calls to the specified user action points
4. Add unit tests for the wrapper module
5. Verify the SDK initializes correctly in the dev environment
6. Create a PR with the changes

## Specifications

- The wrapper module exports: `trackEvent()`, `trackPageView()`, `identify()`
- All functions are typed with TypeScript
- Failed analytics calls log a warning but don't throw
- The SDK is lazy-loaded to avoid blocking page load
- Tests cover: initialization, event tracking, error handling

## Advice

- Check `src/lib/` for existing third-party wrappers to follow the pattern
- The project uses environment variables via `src/config/env.ts`, not `process.env` directly
- Analytics should be disabled in test environments (check `NODE_ENV`)

## Forbidden Actions

- Don't add analytics tracking to test files
- Don't expose the raw SDK — always use the wrapper
- Don't add the API key to the codebase — use the env var

## Required from User

- Which user actions should be tracked (e.g., "sign up", "purchase", "page view")
- The analytics project ID and API key (as a secret)
```

---

## Macros

Assign a **macro** to any playbook — a short identifier starting with `!`:
- Example: `!integrate-analytics`, `!add-endpoint`, `!migrate-db`
- Reference in prompts by typing the macro name
- Must be unique within your organization

---

## Version History

Playbooks maintain version history:
- See previous versions of any playbook
- Compare changes between versions
- Revert to a previous version if needed

---

## Enterprise Playbooks

Enterprise-level playbooks are available across all organizations:
- Created and managed by enterprise admins
- Useful for company-wide standards and procedures
- Can be overridden at the organization level if needed

---

## Tips for Writing Great Playbooks

1. **Start from success** — Write playbooks based on sessions that actually worked
2. **Be explicit about the approach** — Don't let Devin choose the architecture
3. **Include verification steps** — Tell Devin how to confirm the work is correct
4. **Add guardrails** — Forbidden actions prevent the most common mistakes
5. **Keep it focused** — One playbook per task type; don't combine unrelated procedures
6. **Iterate** — Run the playbook 2-3 times and refine based on results
7. **Share** — A playbook that works for you will likely work for your team

---

## Source

- [Creating Playbooks — Devin Docs](https://docs.devin.ai/product-guides/creating-playbooks)
- [Using Playbooks — Devin Docs](https://docs.devin.ai/product-guides/using-playbooks)
