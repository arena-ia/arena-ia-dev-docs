05-api-keys.md# API Keys

## What is an API Key?

Unlike an MCP Server or Custom Action — which give Aria **outbound** access to an external system —, an API Key is the reverse path: it gives an **external system** **inbound** access to an Arena IA instance, without going through a user logged into the browser. It's the same type of access that AI APIs like OpenAI or Anthropic offer.

Use this when you want a chatbot on another platform, a script, or an automation (e.g. n8n) to talk directly to Arena IA.

## ⚙️ How to Create

1. Go to **Administration → API Keys**
2. Click **New Key**
3. Give it a descriptive name (e.g. `Typebot Integration`)
4. Select one or more **groups** the key belongs to — the key inherits the **union** of models and integrations allowed by all selected groups (a key cannot be granted more access than the sum of those groups)
5. Confirm creation

## 🔑 Key Value

The full value (`arena-sk-...`) is shown **only once**, on the creation screen, along with a ready-to-copy call example (`curl`).

> ⚠️ **Save the key somewhere secure as soon as you create it.** It cannot be recovered later — only revoked and replaced with a new one.

## 📡 How to Call

Send the key in the `Authorization` header, in the same format used by AI APIs like OpenAI's:

```
Authorization: Bearer arena-sk-YOUR_VALUE_HERE
```

The request body follows the chat completions pattern (`messages`, `model`, `stream`). Check the `curl` example shown on the key creation screen in your own instance for the exact endpoint and full payload format.

### Which `model` to send

| `model` value | Behavior |
|---|---|
| `arena_smart.aria` / `aria.base` | Routed through the Aria smart router |
| A custom model's **name slug** (e.g. `Fiscal Assistant` → `fiscal-assistant`) or its UUID | Uses that custom model's system prompt, temperature and linked knowledge base. The model must be shared with one of the key's groups — access to the custom model *is* the authorization, it doesn't also need to be in `allowed_models` |
| Any other alias | Passed through to the gateway as-is (must be within the union of the key's `allowed_models`) |

`messages[].content` accepts a plain string or the multimodal array form (`{type:"text"}` / `{type:"image_url"}` / `{type:"file"}`), so a key can send an image or a PDF to a vision-capable model.

> This endpoint is a thin OpenAI-compatible passthrough: no RAG on ad-hoc uploads, no projects, no skills, no `aria.sync` deliberation, and Custom Actions defined on a custom model are **not** executed here.

## 🛠️ Management

- **Edit**: rename the key or adjust which groups it uses at any time — the secret value itself is never shown again or editable, only the name and linked groups.
- **Revoke**: disables access immediately and cannot be reactivated — create a new key if needed.

## ⚠️ Best Practices

- Never expose the real key value in public repositories, logs, or shared models
- Create one key per integration/system — makes it easier to revoke one without affecting the others
- Revoke keys for discontinued integrations
