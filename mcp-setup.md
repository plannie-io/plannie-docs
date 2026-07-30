# Connect an agent (MCP) & API reference

[← plannie.io](https://plannie.io)  ·  [Log in to Plannie](https://app.plannie.io/login)

Public API for agents and integrations. Authenticate with a `fam_live_…` API key as a Bearer token (create one in the app under Settings → Integrations).

API/MCP access requires the **Premium + MCP** plan (or an active trial) — see the "Connect an agent" section below.

## Keys

Keys are created in the app (Settings → Integrations) with a name, one or more scopes, and an optional expiry. A key with no expiry never expires on its own; it can still be revoked at any time. The key list in the app shows each key's derived status (`active` / `expired` / `revoked` / `subscription_lapsed` / `api_plan_required`).

## Errors

Every error is `{ "error": string, "statusCode": number }`. Codes an agent should handle specifically:

| Code | Status | Meaning |
| --- | --- | --- |
| `API key expired` | 401 | The key's `expiresAt` has passed. Create a new key. |
| (invalid/revoked key) | 401 | The key is unknown or was revoked. Create a new key. |
| `subscription_required` | 403 | The household's Plannie subscription has lapsed. All of its keys pause until it resubscribes — no action needed on the key itself, access resumes automatically on renewal. |
| `api_plan_required` | 403 | The household is subscribed but on base Plannie Premium, not **Premium + MCP**. Upgrade in Settings → Subscription & Billing to restore access. |
| `upgrade_required` | 403 | Returned when *creating* a key (`POST /v1/api-keys`) for a household with no active entitlement at all (not even a trial). |

## Connect an agent (MCP)

Any [MCP](https://modelcontextprotocol.io)-compatible client can drive Plannie with the same API keys, two ways:

* **Hosted, no install (recommended):** point the client at `POST https://api.plannie.io/mcp` (streamable HTTP, stateless) with your `fam_live_…` key as a Bearer token.
* **Local npx package:** `npx -y @plannie/mcp` — a stdio server for clients that can't send HTTP headers (or `--http` for a local streamable-HTTP server). See the [@plannie/mcp README](https://github.com/plannie-io/Plannie/tree/main/packages/mcp).

Both expose the same 16 tools (events, tasks, lists, members, activity, meals & recipes) and call straight through this REST API, so scopes, key expiry, rate limits, subscription/plan enforcement, and sparkle attribution all apply exactly as documented above — an agent that gets `api_plan_required` from a REST call will see the same message as an MCP tool error.

In every snippet below, replace `fam_live_…` with your real key.

### Claude Code

```bash
claude mcp add --transport http plannie https://api.plannie.io/mcp \
  --header "Authorization: Bearer fam_live_…"
```

Run inside a project to register it there, or add `--scope user` to make Plannie available in every project. Verify with `/mcp` inside a session, remove with `claude mcp remove plannie`.

### Claude Desktop & claude.ai

Claude Desktop and claude.ai's **custom connector** UI only supports OAuth-authenticated remote servers, which Plannie doesn't offer yet — use the local package instead. Add to `claude_desktop_config.json` (Claude Desktop → Settings → Developer → Edit config; macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`, Windows: `%APPDATA%\Claude\claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "plannie": {
      "command": "npx",
      "args": ["-y", "@plannie/mcp"],
      "env": {
        "PLANNIE_API_URL": "https://api.plannie.io/v1",
        "PLANNIE_API_KEY": "fam_live_…"
      }
    }
  }
}
```

Restart Claude Desktop after saving; the tools appear under the search-and-tools menu.

### Cursor

Add to `~/.cursor/mcp.json` (all projects) or `.cursor/mcp.json` in a repo (that project only):

```json
{
  "mcpServers": {
    "plannie": {
      "url": "https://api.plannie.io/mcp",
      "headers": { "Authorization": "Bearer fam_live_…" }
    }
  }
}
```

Or use the one-click **Add to Cursor** button on [plannie.io/developers](https://plannie.io/developers). Enable the server in Cursor Settings → MCP if it doesn't turn on by itself.

### VS Code

Needs VS Code 1.101+ with MCP support enabled. One-liner:

```bash
code --add-mcp '{"name":"plannie","type":"http","url":"https://api.plannie.io/mcp","headers":{"Authorization":"Bearer fam_live_…"}}'
```

Or add to `.vscode/mcp.json` in your workspace:

```json
{
  "servers": {
    "plannie": {
      "type": "http",
      "url": "https://api.plannie.io/mcp",
      "headers": { "Authorization": "Bearer fam_live_…" }
    }
  }
}
```

### Codex CLI

Codex talks to MCP servers over stdio, so register the local package:

```bash
codex mcp add plannie \
  --env PLANNIE_API_URL=https://api.plannie.io/v1 \
  --env PLANNIE_API_KEY=fam_live_… \
  -- npx -y @plannie/mcp
```

which is equivalent to this in `~/.codex/config.toml`:

```toml
[mcp_servers.plannie]
command = "npx"
args = ["-y", "@plannie/mcp"]
env = { PLANNIE_API_URL = "https://api.plannie.io/v1", PLANNIE_API_KEY = "fam_live_…" }
```

### Any other client

* Speaks **streamable HTTP with custom headers** → use the hosted URL + Bearer header (the Cursor-style config above).
* **stdio only** → `npx -y @plannie/mcp` with the two env vars (the Claude Desktop-style config above), or bridge to the hosted server with [`mcp-remote`](https://www.npmjs.com/package/mcp-remote): `npx -y mcp-remote https://api.plannie.io/mcp --header "Authorization: Bearer fam_live_…"`.

`@plannie/mcp` env reference: `PLANNIE_API_URL` (required, `https://api.plannie.io/v1`), `PLANNIE_API_KEY` (required), `PLANNIE_AGENT_NAME` (optional — the name shown in the family's activity feed, default "MCP agent").

### Troubleshooting

* **401 from `/mcp`** — missing/malformed `Authorization: Bearer fam_live_…` header, or the key was revoked or expired (see the error table above).
* **403 `api_plan_required` / `subscription_required`** — plan issue, not a config issue; see the error table above.
* **Tools connect but calls fail with 403** — the key is missing the scope for that tool (e.g. `create_event` needs `calendar.write`). Check the key's scopes in Settings → Integrations.
* **Client can't send headers** — use the stdio package or `mcp-remote` bridge (see "Any other client").
