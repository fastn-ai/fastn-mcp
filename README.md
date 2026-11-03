# Fastn MCP Gateway

One hosted MCP endpoint for all your tools. Fastn Connect puts any number of MCP servers, app connectors and databases behind a single URL, adds per-user OAuth (each person brings their own account), governance (RBAC/ABAC, PII/PHI redaction, audit logging) and per-tool config, then serves it to Claude, ChatGPT, Cursor, Codex or any MCP client.

There is nothing to install or host. This repository holds no server code: it points to the gateway and its docs.

- **Sign in and connect tools:** https://connect.fastn.dev
- **Documentation:** https://connect.fastn.dev/docs
- **MCP endpoint:** `https://mcp.fastn.dev` (or `https://mcp.fastn.dev/fastn-workspace` to connect straight to your fastn workspace; see [Which URL to use](#which-url-to-use))

> **Using an older guide?** `https://mcp.live.fastn.ai/shttp`, the `pip install fastn-mcp-server` package and its `find_tools` / `execute_tool` / flow tools belong to the retired v1 server. That endpoint no longer serves traffic. Use `https://mcp.fastn.dev` instead. The v1 code is kept on the [`v1` branch](https://github.com/fastn-ai/fastn-mcp/tree/v1) for reference only.

## Quickstart

1. **Connect a tool.** Sign in at [connect.fastn.dev](https://connect.fastn.dev), open **Tools**, then **Browse connectors** to pick an app from the catalog or **Add MCP server** to paste any MCP server's URL. OAuth servers are detected and open a sign-in popup.
2. **Copy your gateway URL** from **Home**. It exposes every enabled tool from every connected server, namespaced as `<connector>__<tool>` (for example `slack__send_message`).
3. **Add it to your client** as a remote MCP server over Streamable HTTP. On first connect the client opens a browser tab where you sign in to Fastn Connect once; there is no token to paste.

Full guide: [Quickstart](https://connect.fastn.dev/docs/quickstart).

## Which URL to use

| URL | What it connects |
|-----|------------------|
| `https://mcp.fastn.dev` | Your org and workspace, taken from your sign-in or access key. On first connect you pick which tools the client gets. |
| `https://mcp.fastn.dev/<org>`, `https://mcp.fastn.dev/<org>/<workspace>` | The same, with the org (or a named workspace in it) named explicitly. Home in Fastn Connect shows this form. |
| `https://mcp.fastn.dev/fastn-workspace` | Only Fastn Workspace: your fastn workflows and the fastn platform tools. No tool picker; signing in is the consent. This is the URL the fastn dashboard hands out. `/<org>/fastn-workspace` pins the org. |
| `https://mcp.fastn.dev/<org>/<workspace>/<app>` | A single app, with no tool picker. |

A trailing `/mcp` is optional on every form. Details: [Gateway API, headers, and access keys](https://connect.fastn.dev/docs/api-reference).

## Connect a client

The examples use `https://mcp.fastn.dev/your-company`; replace it with your URL. Step-by-step setup for every supported client (Claude, Claude Code, ChatGPT, Cursor, VS Code, Codex, Antigravity, Devin, Cline, Lovable, Bolt, Zed) is in [Connect clients](https://connect.fastn.dev/docs/connect-clients).

**Claude Code**

```bash
claude mcp add --transport http fastn https://mcp.fastn.dev/your-company
claude mcp login fastn
```

**Claude / ChatGPT:** add a custom connector (Claude: Customize > Connectors > Add custom connector; ChatGPT: Plugins > Add > Add custom MCP server), paste the URL, leave authentication on OAuth, then Connect.

**Cursor** (`~/.cursor/mcp.json`)

```json
{
  "mcpServers": {
    "fastn": { "url": "https://mcp.fastn.dev/your-company" }
  }
}
```

**VS Code** (`.vscode/mcp.json`)

```json
{
  "servers": {
    "fastn": { "type": "http", "url": "https://mcp.fastn.dev/your-company" }
  }
}
```

**Codex**

```bash
codex mcp add fastn --url https://mcp.fastn.dev/your-company
```

### Clients that cannot sign in: access keys

An admin creates an access key from **Connect a client** on Home or the **Agents** page. Send it as a bearer token:

```bash
claude mcp add --transport http fastn https://mcp.fastn.dev/your-company \
  --header "Authorization: Bearer ucl_<access-key>"
```

### Headless and multi-tenant agents

One agent key can serve many tenants and end users. Pass the tenant and the user to act as on each client:

```python
from langchain_mcp_adapters.client import MultiServerMCPClient

client = MultiServerMCPClient({
    "fastn": {
        "transport": "streamable_http",
        "url": "https://mcp.fastn.dev/your-company",
        "headers": {
            "Authorization": "Bearer ucl_<agent-key>",
            "X-Org-Id": "acme",
            "X-User-Id": "user@acme.com",
        },
    }
})
```

See [Multi-tenant agents](https://connect.fastn.dev/docs/multi-tenant-agents) and [Gateway API, headers, and access keys](https://connect.fastn.dev/docs/api-reference).

## Docs

| Topic | Link |
|-------|------|
| What is an MCP gateway? | https://connect.fastn.dev/docs/what-is-an-mcp-gateway |
| Per-user OAuth | https://connect.fastn.dev/docs/per-user-oauth |
| Connector auth modes | https://connect.fastn.dev/docs/connector-auth-modes |
| Multi-tenancy and authentication | https://connect.fastn.dev/docs/multi-tenancy-and-auth |
| Organizations, teams, and roles | https://connect.fastn.dev/docs/organizations-and-teams |
| Governance: RBAC, redaction, per-tool config | https://connect.fastn.dev/docs/governance |
| Tools and databases | https://connect.fastn.dev/docs/tools-workflows-databases |

The docs at connect.fastn.dev are the source of truth. If this README and the docs disagree, the docs win.

## Support

Open an issue in this repository or reach us through [fastn.ai](https://fastn.ai).

## License

MIT
