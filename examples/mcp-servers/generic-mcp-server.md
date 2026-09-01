# Exemplo: Servidor MCP Genérico (dialeto simplificado) / Generic MCP Server (simplified dialect)

**Tipo:** Servidor MCP | **Auth:** Bearer Token | **Linguagem:** Node.js

> ℹ️ Este exemplo usa o **dialeto HTTP simplificado da Arena** (`POST /mcp` com `method`/`params`/`id`
> à mão), útil quando você não pode usar um SDK MCP. Para um servidor novo, prefira o transporte
> **oficial** — ver [official-streamable-http.md](official-streamable-http.md). Ao cadastrar este
> servidor, deixe o **Protocolo** em `Auto` (a Arena cai para o dialeto simplificado sozinha) ou
> force `HTTP simplificado (Arena)`.
>
> ℹ️ This example uses Arena's **simplified HTTP dialect** (hand-rolled `POST /mcp` with
> `method`/`params`/`id`), handy when you can't use an MCP SDK. For a new server, prefer the
> **official** transport — see [official-streamable-http.md](official-streamable-http.md). When
> registering, leave **Protocol** on `Auto` or force `Simplified HTTP (Arena)`.

---

## PT-BR

Implementação mínima de um servidor MCP compatível com a Arena IA.

```javascript
// server.js — Servidor MCP mínimo (Node.js + Express)
const express = require('express');
const app = express();
app.use(express.json());

// Middleware de autenticação
app.use((req, res, next) => {
  const auth = req.headers['authorization'];
  if (auth !== 'Bearer SEU_TOKEN_SECRETO') {
    return res.status(401).json({ error: 'Unauthorized' });
  }
  next();
});

// Endpoint MCP principal
app.post('/mcp', (req, res) => {
  const { method, params, id } = req.body;

  // Anuncia as ferramentas disponíveis
  if (method === 'tools/list') {
    return res.json({
      jsonrpc: '2.0',
      id,
      result: {
        tools: [
          {
            name: 'minha_ferramenta',
            description: 'Descrição clara da ferramenta para o modelo de IA',
            inputSchema: {
              type: 'object',
              properties: {
                entrada: {
                  type: 'string',
                  description: 'Parâmetro de entrada da ferramenta'
                }
              },
              required: ['entrada']
            }
          }
        ]
      }
    });
  }

  // Executa a ferramenta chamada
  if (method === 'tools/call') {
    const { name, arguments: args } = params;

    if (name === 'minha_ferramenta') {
      const resultado = `Processado: ${args.entrada}`;

      return res.json({
        jsonrpc: '2.0',
        id,
        result: {
          content: [
            { type: 'text', text: resultado }
          ]
        }
      });
    }
  }

  // Método não encontrado
  res.status(404).json({
    jsonrpc: '2.0',
    id,
    error: { code: -32601, message: 'Method not found' }
  });
});

app.listen(3000, () => console.log('MCP Server rodando na porta 3000'));
```

---

## EN

Minimal MCP server implementation compatible with Arena IA.

```javascript
// server.js — Minimal MCP Server (Node.js + Express)
const express = require('express');
const app = express();
app.use(express.json());

// Authentication middleware
app.use((req, res, next) => {
  const auth = req.headers['authorization'];
  if (auth !== 'Bearer YOUR_SECRET_TOKEN') {
    return res.status(401).json({ error: 'Unauthorized' });
  }
  next();
});

// Main MCP endpoint
app.post('/mcp', (req, res) => {
  const { method, params, id } = req.body;

  // Announce available tools
  if (method === 'tools/list') {
    return res.json({
      jsonrpc: '2.0',
      id,
      result: {
        tools: [
          {
            name: 'my_tool',
            description: 'Clear description of the tool for the AI model',
            inputSchema: {
              type: 'object',
              properties: {
                input: {
                  type: 'string',
                  description: 'Tool input parameter'
                }
              },
              required: ['input']
            }
          }
        ]
      }
    });
  }

  // Execute the called tool
  if (method === 'tools/call') {
    const { name, arguments: args } = params;

    if (name === 'my_tool') {
      const result = `Processed: ${args.input}`;

      return res.json({
        jsonrpc: '2.0',
        id,
        result: {
          content: [
            { type: 'text', text: result }
          ]
        }
      });
    }
  }

  // Method not found
  res.status(404).json({
    jsonrpc: '2.0',
    id,
    error: { code: -32601, message: 'Method not found' }
  });
});

app.listen(3000, () => console.log('MCP Server running on port 3000'));
```
