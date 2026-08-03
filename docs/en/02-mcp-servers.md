# MCP Servers

## What is an MCP Server?

An **MCP Server** is an external service that implements the **Model Context Protocol (MCP)** — an open standard based on JSON-RPC 2.0 that defines how tools are announced and called by language models.

When registering an MCP server in Arena IA, the platform performs **automatic discovery** of all exposed tools, making them available to users in the configured group.

---

## 🔄 Communication Flow

```
Arena IA → POST /mcp (tools/list)   → MCP Server
Arena IA ← { tools: [...] }         ← MCP Server

[user sends a message]

Arena IA → POST /mcp (tools/call)   → MCP Server
          { name, arguments }
Arena IA ← { content: [...] }       ← MCP Server
```

---

## ⚙️ How to Register

1. Go to **Administration → MCP Servers**
2. Click **New Server**
3. Fill in the fields:
   - **Name:** friendly identifier
   - **URL:** MCP server endpoint
   - **Authentication:** None, API Key or Bearer Token
4. Click **Test Connection**
5. Confirm discovered tools
6. Set the **scope** (group or global) and activate

---

## 🔐 Supported Authentication

| Type | Sent header |
|---|---|
| None | — |
| API Key | `X-Api-Key: {your_key}` |
| Bearer Token | `Authorization: Bearer {your_token}` |

---

## 📋 MCP Server Requirements

Your server must implement at least two methods:

### `tools/list`

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
            "sku": {
              "type": "string",
              "description": "Product SKU code"
            }
          },
          "required": ["sku"]
        }
      }
    ]
  }
}
```

### `tools/call`

```json
{
  "jsonrpc": "2.0",
  "id": 2,
  "result": {
    "content": [
      {
        "type": "text",
        "text": "Inventory for SKU ABC123: 47 units available."
      }
    ]
  }
}
```

---

## 🔁 Tool Rediscovery

When the MCP server adds new tools, the administrator must click **Rediscover** in the panel to update the list — no need to reconfigure the connection.

---

## ⚠️ Best Practices

- Keep tool `descriptions` clear and objective — the model uses this text to decide when to call the tool
- Return descriptive error messages in the `content` field
- Use HTTPS in production
- Implement rate limiting on your server
