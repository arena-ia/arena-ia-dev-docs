# Exemplo: Servidor MCP Oficial (Streamable HTTP) / Official MCP Server (Streamable HTTP)

**Tipo:** Servidor MCP | **Transporte:** Streamable HTTP (spec MCP 2025-06-18) | **Linguagem:** Node.js / Python

Este é o formato **recomendado** para qualquer servidor novo. Use um SDK oficial — ele cuida do
handshake `initialize`, das sessões, do SSE e da paginação de `tools/list`.

This is the **recommended** format for any new server. Use an official SDK — it handles the
`initialize` handshake, sessions, SSE and `tools/list` pagination.

---

## Node.js — `@modelcontextprotocol/sdk`

```javascript
// npm i @modelcontextprotocol/sdk express zod
import express from "express";
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StreamableHTTPServerTransport } from "@modelcontextprotocol/sdk/server/streamableHttp.js";
import { z } from "zod";

const mcp = new McpServer({ name: "meu-servidor", version: "1.0.0" });

mcp.registerTool(
  "consultar_estoque", // check_inventory
  {
    description: "Consulta o estoque de um produto pelo SKU / Checks product inventory by SKU",
    inputSchema: { sku: z.string().describe("Código SKU do produto / Product SKU code") },
  },
  async ({ sku }) => {
    // sua lógica aqui / your logic here
    const unidades = 47;
    return { content: [{ type: "text", text: `SKU ${sku}: ${unidades} unidades / units` }] };
  }
);

const app = express();
app.use(express.json());

// Autenticação opcional (Bearer estático) / optional static Bearer auth
app.use((req, res, next) => {
  if (req.headers.authorization !== "Bearer SEU_TOKEN_SECRETO / YOUR_SECRET_TOKEN") {
    return res.status(401).json({ error: "Unauthorized" });
  }
  next();
});

// Endpoint MCP único / single MCP endpoint
app.all("/mcp", async (req, res) => {
  const transport = new StreamableHTTPServerTransport({ sessionIdGenerator: undefined });
  res.on("close", () => transport.close());
  await mcp.connect(transport);
  await transport.handleRequest(req, res, req.body);
});

app.listen(process.env.PORT || 3000, () => console.log("MCP em / on /mcp"));
```

---

## Python — `mcp` / FastMCP

```python
# pip install "mcp[cli]"
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("meu-servidor")

@mcp.tool()
async def consultar_estoque(sku: str) -> str:
    """Consulta o estoque de um produto pelo SKU / Checks product inventory by SKU"""
    unidades = 47  # sua lógica / your logic
    return f"SKU {sku}: {unidades} unidades / units"

if __name__ == "__main__":
    # expõe POST/GET em /mcp / serves POST/GET on /mcp
    mcp.run(transport="streamable-http")
```

---

## Registrar na Arena IA / Register in Arena IA

1. **Administração → Servidores MCP → Novo Servidor**
2. **URL:** `https://seu-servidor.com/mcp`
3. **Protocolo:** `Auto` (ou `MCP (oficial)`)
4. **Autenticação:** `Bearer Token` (cole o token) — ou `Nenhuma` se o servidor for público
5. **Testar Conexão** → confirme as ferramentas descobertas → ative

> Para servidores que exigem login por usuário, escolha **Autenticação = OAuth** e siga a seção
> "OAuth (login do usuário)" em [docs/pt/02-servidores-mcp.md](../../docs/pt/02-servidores-mcp.md).
