# MCP Servers

## What is an MCP Server?

An **MCP Server** is an external service that implements the **Model Context Protocol (MCP)** — an open standard, maintained by Anthropic, that defines how tools are announced and called by language models.

When you register an MCP server in Arena IA, the platform performs **automatic discovery** of every exposed tool and makes them available to users in the configured group (or globally).

---

## 🔌 Supported Transports

Arena IA speaks the **official MCP transport** and also keeps a legacy dialect for backwards compatibility.

| Transport | Supported | Notes |
|---|---|---|
| **Streamable HTTP** (MCP spec 2025-06-18) | ✅ | Single endpoint, `initialize` handshake, `Mcp-Session-Id`, `application/json` or SSE responses. **Recommended for any new server.** |
| **HTTP + SSE** (legacy MCP transport, pre-2025-03) | ✅ (automatic fallback) | Used only if the Streamable HTTP handshake fails |
| **Arena simplified HTTP dialect** | ✅ (legacy) | `GET /tools` + `POST /tools/call` — see [Legacy dialect](#-legacy--arena-simplified-http-dialect). Still used by no-code platforms and webhook-based servers |
| **stdio** | ❌ | Local-only transport (subprocess) — not applicable to a remote server registered by URL |

Arena IA's MCP client uses the official `@modelcontextprotocol/sdk`.

---

## ⚙️ How to Register

1. Go to **Administration → MCP Servers**
2. Click **New Server**
3. Fill in the fields:
   - **Name / Description** — free text
   - **URL** — the single MCP endpoint (e.g. `https://mcp.example.com/mcp`). Do **not** append route suffixes; the client talks to this URL directly
   - **Scope** — Global (all groups) or a specific group
   - **Protocol** — leave on **Auto** (tries the official protocol, falls back to the simplified dialect if the handshake fails). Force **MCP (official)** or **Simplified HTTP (Arena)** only if you know you need to
   - **Authentication** — None, API Key, Bearer Token or OAuth (see below)
4. Click **Save** — automatic discovery lists the exposed tools (name, description, parameters)
5. Click **Test Connection** to confirm the server responds
6. Set the server **active**

Whenever the server changes its tool set, click **Rediscover** — the local list does **not** update on its own.

> 💡 Before registering, validate your endpoint with the [MCP Inspector](https://github.com/modelcontextprotocol/inspector).

---

## 🔐 Supported Authentication

| Type | How it's sent |
|---|---|
| **None** | — (public MCP servers) |
| **API Key** | Custom header — you provide `Header-Name:value` (e.g. `X-Api-Key:abc123`) |
| **Bearer Token** | `Authorization: Bearer {your_static_token}` (PAT / server API key) |
| **OAuth (user login)** | Interactive OAuth 2.1 authorization-code flow, per user — see below |

### OAuth (user login)

For servers that require each person to sign in with their own account (GitHub, Linear, remote Notion, Salesforce, etc.):

1. Register the server with **Authentication = OAuth** and **Protocol = Auto** (or MCP official).
2. On the admin screen, click **Authorize** to connect **your own** account. This also unlocks tool discovery (the discovered tool list is shared across all users).
3. Each end user then connects their own account in **Settings → Integrations → MCP Servers**. Until they connect, calling a tool from that server makes the assistant reply with instructions to connect first.

Behind the scenes the SDK performs RFC 9728 resource discovery, **Dynamic Client Registration** (RFC 7591) and **PKCE** automatically. Tokens are stored per (user, server), encrypted with AES-256-GCM, and refreshed transparently. Only the *authorization code* + *refresh token* grant is supported (no client-credentials, no private-key JWT).

**Server requirements:** expose `/.well-known/oauth-protected-resource` (RFC 9728) and an authorization server with metadata (RFC 8414 / OIDC Discovery). The redirect URI Arena uses is `https://YOUR-DOMAIN/api/mcp-servers/oauth/callback`.

### OAuth with a pre-registered client (servers without DCR)

Some servers **do not accept** Dynamic Client Registration and require an OAuth app registered manually. In that case fill in these extra fields on the server form (they only appear when Authentication = OAuth):

| Field | When to fill it |
|---|---|
| **OAuth Client ID** | Whenever the server does not support DCR — this disables dynamic registration |
| **OAuth Client Secret** | Only if the app is *confidential* (the server issued a secret) |
| **Scopes** | When the server requires explicit scopes (space-separated) |

> ⚠️ Changing the Client ID or Secret later **invalidates existing connections** — users have to click **Connect** again.

**Example — Salesforce Hosted MCP:**

1. In your Salesforce org, create an **External Client App** (Connected Apps do not work for MCP) with OAuth enabled, scopes `mcp_api` and `refresh_token`, and callback URL `https://YOUR-DOMAIN/api/mcp-servers/oauth/callback`.
2. Note the **Consumer Key** (= Client ID) and **Consumer Secret**.
3. In Arena: **New Server** → the org's MCP endpoint URL, Protocol `Auto`, Authentication `OAuth`, and paste Client ID + Secret + Scopes `mcp_api refresh_token`.
4. Click **Authorize** → Salesforce login → back, connected. **Rediscover** lists the tools. Each user connects their own account in Integrations; Salesforce applies each person's own permissions.

---

## 📋 Building a Standard MCP Server

Use an official SDK — do not implement the protocol by hand. Expose the **Streamable HTTP** transport; the SDK handles `initialize`, sessions, SSE and `tools/list` pagination for you.

### TypeScript (`@modelcontextprotocol/sdk`)

```javascript
import express from "express";
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StreamableHTTPServerTransport } from "@modelcontextprotocol/sdk/server/streamableHttp.js";
import { z } from "zod";

const mcp = new McpServer({ name: "my-server", version: "1.0.0" });

mcp.registerTool(
  "check_inventory",
  {
    description: "Checks product inventory by SKU code",
    inputSchema: { sku: z.string().describe("Product SKU code") },
  },
  async ({ sku }) => {
    const units = await lookupInventory(sku); // your logic
    return { content: [{ type: "text", text: `SKU ${sku}: ${units} units available.` }] };
  }
);

const app = express();
app.use(express.json());
app.all("/mcp", async (req, res) => {
  const transport = new StreamableHTTPServerTransport({ sessionIdGenerator: undefined });
  res.on("close", () => transport.close());
  await mcp.connect(transport);
  await transport.handleRequest(req, res, req.body);
});
app.listen(process.env.PORT || 3000);
```

### Python (`mcp` / FastMCP)

```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("my-server")

@mcp.tool()
async def check_inventory(sku: str) -> str:
    """Checks product inventory by SKU code"""
    units = await lookup_inventory(sku)  # your logic
    return f"SKU {sku}: {units} units available."

if __name__ == "__main__":
    mcp.run(transport="streamable-http")  # serves POST/GET on /mcp
```

Full documentation: [modelcontextprotocol.io](https://modelcontextprotocol.io).

---

## 🕸️ Legacy — Arena Simplified HTTP Dialect

Before official-protocol support, Arena IA spoke its own simplified dialect. It's still accepted and is what no-code / webhook-based servers (e.g. Zapier, n8n) use. Force it with **Protocol = Simplified HTTP (Arena)**.

| Purpose | Request | 2xx Response |
|---|---|---|
| Discovery | `GET {base}/tools` (fallback `POST {base}/tools/list` with a JSON-RPC body) | `{ "tools": [ { "name", "description", "inputSchema": {…} } ] }` |
| Execution | `POST {base}/tools/call` with `{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"<tool>","arguments":{…}}}` | `{ "content": [ { "type": "text", "text": "<result>" } ] }` |
| Health | `GET {base}/health` (optional) | any 2xx or 405 |

### `tools/list` example

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "tools": [
      {
        "name": "check_inventory",
        "description": "Checks product inventory by SKU code",
        "inputSchema": {
          "type": "object",
          "properties": {
            "sku": { "type": "string", "description": "Product SKU code" }
          },
          "required": ["sku"]
        }
      }
    ]
  }
}
```

### `tools/call` example

```json
{
  "jsonrpc": "2.0",
  "id": 2,
  "result": {
    "content": [
      { "type": "text", "text": "Inventory for SKU ABC123: 47 units available." }
    ]
  }
}
```

---

## 🔁 Tool Rediscovery

When the MCP server adds or changes tools, the administrator must click **Rediscover** in the panel — no need to reconfigure the connection or re-authorize.

---

## ⚠️ Execution Limits & Best Practices

- **Timeout:** each tool call has a **30-second** ceiling. A slow tool should return a link and serve the result separately.
- **Network:** the host must be **public** — internal addresses (localhost, `10.x`, `192.168.x`, link-local) are blocked (SSRF protection).
- **Agent loop:** MCP tools enable the multi-step agent loop automatically, so chained tools (e.g. "list, then read") work in a single message.
- Keep tool `descriptions` clear and specific — the model uses this text to decide when to call the tool, and Arena's router only offers a tool when the user's message has words matching its name/description.
- Return descriptive error messages in the `content` field so the model can relay failures to the user.
- Use HTTPS in production and implement rate limiting on your server.

---

## 🩺 Troubleshooting

| Symptom | Likely cause |
|---|---|
| "No tools discovered" | Server changed its tools → click **Rediscover**; or an OAuth server with nobody authorized → click **Authorize** |
| Tool discovered but the model never uses it | Name/description too vague — Arena's router only surfaces a tool when the user's message matches its name/description |
| "Authorization expired" in chat | Token refresh failed → the user reconnects in Settings → Integrations |
| OAuth flow returns an error | `redirect_uri` not registered on the authorization server; server clock skew; `iss` mismatch |
| Timeout on a slow tool | 30-second ceiling per call — return a link and serve the result out of band |
| Network error on Test Connection | Host is not public (SSRF block) |
