# Arena IA Platform Overview

The Arena IA platform allows contracted companies to extend AI assistant capabilities through external integrations. There are two main approaches to connect services and APIs to the platform: **MCP Servers** and **Custom Model Actions**.

---

## 🏗️ Integration Architecture

```
┌─────────────────────────────────────┐
│            Arena IA                 │
│                                     │
│  ┌─────────────┐  ┌──────────────┐  │
│  │  MCP Server │  │Custom Action │  │
│  └──────┬──────┘  └──────┬───────┘  │
│         │                │          │
└─────────┼────────────────┼──────────┘
          │                │
     MCP Protocol       REST HTTP
          │                │
   ┌──────▼──────┐  ┌──────▼───────┐
   │  External   │  │  External    │
   │ MCP Server  │  │  REST API    │
   └─────────────┘  └──────────────┘
```

---

## 🔑 Core Concepts

### Function Calling
Both approaches use **function calling** — the mechanism by which the language model decides, during the conversation, when and how to call an external tool. The API result is incorporated into the AI response transparently.

### MCP Servers
- Based on the **Model Context Protocol (MCP)**, an open standard maintained by Anthropic
- Configured by the platform **administrator**
- Expose multiple tools at once via automatic discovery
- Scope: **user group** or **global**

### Custom Model Actions
- Based on simple **REST HTTP** calls
- Configured by the **custom model creator**
- Each action is manually defined via a JSON block
- Scope: **specific model** where it was configured

---

## 📊 Quick Comparison

| Criteria | MCP Server | Custom Action |
|---|---|---|
| Protocol | MCP (JSON-RPC 2.0) | REST HTTP |
| Configured by | Administrator | Model creator |
| Scope | Group / Global | Specific model |
| Tool discovery | Automatic | Manual (JSON) |
| Multiple tools | Yes | One per block |
| Best for | MCP-compatible platforms | Specific REST APIs |

---

## 🗺️ Where to start?

- Does your service already expose an MCP endpoint? → [MCP Servers](02-mcp-servers.md)
- Want to connect a REST API to a specific assistant? → [Custom Actions](03-custom-actions.md)
- Looking for ready-to-use examples? → [examples folder](../../examples/)
