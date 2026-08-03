# Arena IA Platform Overview

The Arena IA platform allows contracted companies to extend AI assistant capabilities through external integrations. There are three main approaches to connect services and APIs to the platform: **MCP Servers**, **Custom Model Actions**, and **API Keys** (for the reverse direction — external systems calling into Arena IA).

---

## 🏗️ Integration Architecture

```
┌─────────────────────────────────────┐
│              Arena IA                │
│                                       │
│  ┌─────────────┐  ┌──────────────┐  │
│  │ MCP Server  │  │Custom Action │  │
│  └──────┬──────┘  └──────┬───────┘  │
│         │                │          │
└─────────┼────────────────┼──────────┘
          │                │
     MCP Protocol      REST HTTP
          │                │
   ┌──────▼──────┐  ┌──────▼───────┐
   │  External   │  │  External    │
   │ MCP Server  │  │  REST API    │
   └─────────────┘  └──────────────┘
```

## 🔑 Core Concepts

### Function Calling

Both MCP Servers and Custom Model Actions use function calling — the mechanism by which the language model decides, during the conversation, when and how to call an external tool. The API result is incorporated into the AI response transparently.

> ⚠️ **Execution limits:** each function call has a 15-second timeout, and internal network addresses (outside the platform's own services) are blocked by default.


### MCP Servers

- Based on the Model Context Protocol (MCP), an open standard maintained by Anthropic
- Configured by the platform administrator
- Expose multiple tools at once via automatic discovery
- Scope: user group or global

### Custom Model Actions

- Based on simple REST HTTP calls
- Configured by the custom model creator
- Each action is manually defined via a JSON block
- Scope: specific model where it was configured

### API Keys

- Reverse direction: gives an external system inbound access to Arena IA
- Configured by the platform administrator
- Uses the same access pattern as AI APIs like OpenAI or Anthropic
- Scope: one or more groups (key inherits the union of their permissions)

---

## 📊 Quick Comparison

The table below compares **MCP Servers** and **Custom Model Actions**, since both are outbound (Arena IA calling an external service). **API Keys** work differently — they are the reverse, inbound direction — so they don't fit this table; see [API Keys](05-api-keys.md) for details.

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

- Does your service already expose an MCP endpoint? → MCP Servers
- Want to connect a REST API to a specific assistant? → Custom Actions
- Want an external system to call into Arena IA directly? → API Keys
- Looking for ready-to-use examples? → examples folder
