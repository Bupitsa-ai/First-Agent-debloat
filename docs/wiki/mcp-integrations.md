# MCP Integrations

> Model Context Protocol (MCP) — connecting Devin to external tools and data sources.

## What is MCP?

MCP is an **open protocol** that enables Devin to use hundreds of external tools and data sources. It acts as a universal adapter between Devin and third-party services.

Devin supports three transport methods:
- **stdio** — Standard input/output (local processes)
- **SSE** — Server-Sent Events (remote streaming)
- **HTTP** — Standard HTTP requests (remote request/response)

## Why Use MCP?

With MCP integrations, Devin can:

| Category | Examples |
|----------|---------|
| **Monitoring & Logs** | Dig through Sentry, Datadog, and Vercel logs |
| **Data Analysis** | Use Devin as a data analyst in Slack with database MCPs |
| **Issue Tracking** | Investigate SonarQube, CircleCI, and Jam issues |
| **Content Creation** | Bulk create Linear tickets, Notion docs, Google Docs (via Zapier) |
| **Business Tools** | Pull context from Airtable, Stripe, and HubSpot |
| **Design** | Read designs from Figma |
| **Databases** | Query Postgres, Supabase, Redis, Neon, Pinecone |
| **Deployment** | Interact with Vercel, Netlify, Heroku, Docker Hub |

## Getting Started

1. Navigate to **Settings > MCP Marketplace** in the Devin app
2. Browse available integrations
3. Click to enable an MCP
4. For OAuth-based MCPs, complete the authentication flow when prompted

> **Recommendation**: Use a **service account** (not personal) for OAuth MCPs. Access is shared within your organization.

## Marketplace MCPs

### Popular Integrations

| MCP | Category | Use Case |
|-----|----------|----------|
| **Datadog** | Monitoring | Query metrics, investigate incidents |
| **Sentry** | Error Tracking | Triage errors, find root causes |
| **Slack** | Communication | Read channels, post updates |
| **Figma** | Design | Pull design specs and assets |
| **Stripe** | Payments | Query transactions, manage subscriptions |
| **Supabase** | Database | Query and manage Supabase databases |
| **Postgres** | Database | Direct database queries |
| **Vercel** | Deployment | Manage deployments, check logs |
| **Netlify** | Deployment | Deploy and manage sites |
| **Linear** | Project Management | Create and manage tickets |
| **Notion** | Knowledge Management | Read and write Notion pages |
| **Zapier** | Automation | Connect to 5,000+ apps |
| **Airtable** | Data Management | Query and update Airtable bases |
| **Docker Hub** | Containers | Manage Docker images |
| **SonarQube** | Code Quality | Check code quality metrics |
| **CircleCI** | CI/CD | Monitor and trigger pipelines |
| **Playwright** | Testing | Browser automation and testing |
| **ElasticSearch** | Search | Query ElasticSearch indices |
| **Pinecone** | Vector DB | Manage vector embeddings |
| **Redis** | Cache/DB | Query Redis instances |

## Setting Up a Custom MCP Server

If your needed MCP isn't in the marketplace, use the **"Add Your Own"** option.

### STDIO Transport

For local processes that communicate via stdin/stdout:

```json
{
  "command": "npx",
  "args": ["-y", "@my-org/my-mcp-server"],
  "env": {
    "API_KEY": "your-api-key"
  }
}
```

### SSE Transport

For remote servers using Server-Sent Events:

```json
{
  "url": "https://my-mcp-server.example.com/sse",
  "headers": {
    "Authorization": "Bearer your-token"
  }
}
```

### HTTP Transport

For standard HTTP request/response servers:

```json
{
  "url": "https://my-mcp-server.example.com/mcp",
  "headers": {
    "Authorization": "Bearer your-token"
  }
}
```

## Common Patterns

### Connecting to an Internal API

```json
{
  "command": "node",
  "args": ["./mcp-server.js"],
  "env": {
    "INTERNAL_API_URL": "https://api.internal.example.com",
    "API_TOKEN": "${INTERNAL_API_TOKEN}"
  }
}
```

### Connecting to a Database

```json
{
  "command": "npx",
  "args": ["-y", "@modelcontextprotocol/server-postgres"],
  "env": {
    "DATABASE_URL": "${DATABASE_URL}"
  }
}
```

### Using Environment Variables for Secrets

Reference secrets using `${VAR_NAME}` syntax. Configure secrets in **Settings > Secrets** in the Devin app. Never hardcode credentials in MCP configurations.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Test listing tools" fails | Check server logs; verify the command or URL is correct |
| Server connects but tools unavailable | Ensure your server implements the MCP protocol correctly |
| OAuth authentication issues | Try disconnecting and reconnecting; use a service account |
| Timeout errors | Check network connectivity; verify the server is running |

### General Debugging Tips

1. Use "Test listing tools" in the MCP settings to verify connectivity
2. Check that required environment variables are set
3. Verify the server responds to MCP protocol messages
4. Check logs in the session shell for error details

---

## Source

- [MCP Marketplace — Devin Docs](https://docs.devin.ai/work-with-devin/mcp)
- [DeepWiki MCP](https://docs.devin.ai/work-with-devin/deepwiki-mcp)
- [Devin MCP](https://docs.devin.ai/work-with-devin/devin-mcp)
