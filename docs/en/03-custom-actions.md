# Custom Model Actions

## What is a Custom Action?

A **Custom Action** is a REST API integration defined directly in an Arena IA custom model via a JSON block. It allows the assistant to call external endpoints during the conversation using the **function calling** mechanism.

---

## 🔄 Communication Flow

```
User → Message → Arena IA
Arena IA → detects intent → calls function
Arena IA → HTTP Request → External API
Arena IA ← HTTP Response ← External API
Arena IA → enriched response → User
```

---

## ⚙️ How to Configure

1. Go to **Productivity → My Models**
2. Select the desired model
3. In the **Actions** section, click **Advanced**
4. Paste the action JSON block
5. Save the model

---

## 📋 JSON Structure

```json
[
  {
    "type": "function",
    "function": {
      "name": "function_name",
      "description": "Clear description of what the function does and when to use it",
      "parameters": {
        "type": "object",
        "properties": {
          "parameter1": {
            "type": "string",
            "description": "Parameter description"
          }
        },
        "required": ["parameter1"]
      }
    },
    "_url": "https://api.example.com/endpoint",
    "_method": "GET",
    "_param_location": "query",
    "_headers": {
      "Authorization": "Bearer YOUR_TOKEN"
    }
  }
]
```

---

## 🔧 Available Fields

| Field | Required | Description |
|---|---|---|
| `type` | ✅ | Always `"function"` |
| `function.name` | ✅ | Unique function name (no spaces) |
| `function.description` | ✅ | Description used by the model to decide when to call |
| `function.parameters` | ✅ | Parameter JSON Schema |
| `_url` | ✅ | Endpoint base URL |
| `_method` | ✅ | `GET`, `POST`, `PUT`, `PATCH` or `DELETE` |
| `_param_location` | ✅ | `query` (GET) or `body` (POST/PUT) |
| `_headers` | ❌ | Additional HTTP headers (e.g. authentication) |

---

## 🔐 Authentication

### Bearer Token
```json
"_headers": {
  "Authorization": "Bearer YOUR_TOKEN_HERE"
}
```

### API Key in Header
```json
"_headers": {
  "X-Api-Key": "YOUR_KEY_HERE"
}
```

### API Key in URL (query param)
Include directly in `_url`:
```json
"_url": "https://api.example.com/data?api_key=YOUR_KEY"
```

---

## ⚠️ Best Practices

- Write detailed `descriptions` — they guide the model in deciding when to use the action
- Use descriptive function names: `get_order_status` is better than `api1`
- Only mark fields as `required` if they are truly mandatory
- Always prefer HTTPS
- Avoid exposing sensitive tokens in shared models
