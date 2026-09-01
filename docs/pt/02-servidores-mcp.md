# Servidores MCP

## O que é um Servidor MCP?

Um **Servidor MCP** é um serviço externo que implementa o protocolo **Model Context Protocol (MCP)** — padrão aberto, mantido pela Anthropic, que define como ferramentas são anunciadas e chamadas por modelos de linguagem.

Ao registrar um servidor MCP na Arena IA, a plataforma faz a **descoberta automática** de todas as ferramentas expostas e as disponibiliza para os usuários do grupo configurado (ou globalmente).

---

## 🔌 Transportes Suportados

A Arena IA fala o **transporte MCP oficial** e mantém um dialeto legado para compatibilidade.

| Transporte | Suportado | Observações |
|---|---|---|
| **Streamable HTTP** (spec MCP 2025-06-18) | ✅ | Endpoint único, handshake `initialize`, `Mcp-Session-Id`, resposta `application/json` ou SSE. **Recomendado para qualquer servidor novo.** |
| **HTTP + SSE** (transporte MCP legado, pré-2025-03) | ✅ (fallback automático) | Usado só se o handshake do Streamable HTTP falhar |
| **Dialeto HTTP simplificado da Arena** | ✅ (legado) | `GET /tools` + `POST /tools/call` — ver [Dialeto legado](#-legado--dialeto-http-simplificado-da-arena). Ainda usado por plataformas no-code e servidores baseados em webhook |
| **stdio** | ❌ | Transporte local (subprocesso) — não se aplica a um servidor remoto cadastrado por URL |

O cliente MCP da Arena IA usa o SDK oficial `@modelcontextprotocol/sdk`.

---

## ⚙️ Como Registrar

1. Acesse **Administração → Servidores MCP**
2. Clique em **Novo Servidor**
3. Preencha os campos:
   - **Nome / Descrição** — texto livre
   - **URL** — o endpoint MCP único (ex.: `https://mcp.exemplo.com/mcp`). **Não** acrescente sufixos de rota; o cliente fala com essa URL diretamente
   - **Escopo** — Global (todos os grupos) ou um grupo específico
   - **Protocolo** — deixe em **Auto** (tenta o protocolo oficial e cai para o dialeto simplificado se o handshake falhar). Force **MCP (oficial)** ou **HTTP simplificado (Arena)** só se souber que precisa
   - **Autenticação** — Nenhuma, API Key, Bearer Token ou OAuth (ver abaixo)
4. Clique em **Salvar** — a descoberta automática lista as ferramentas expostas (nome, descrição, parâmetros)
5. Clique em **Testar Conexão** para confirmar que o servidor responde
6. Marque o servidor como **ativo**

Sempre que o servidor mudar o conjunto de ferramentas, clique em **Redescobrir** — a lista local **não** atualiza sozinha.

> 💡 Antes de cadastrar, valide seu endpoint com o [MCP Inspector](https://github.com/modelcontextprotocol/inspector).

---

## 🔐 Autenticação Suportada

| Tipo | Como é enviado |
|---|---|
| **Nenhuma** | — (servidores MCP públicos) |
| **API Key** | Header personalizado — você informa `Nome-Do-Header:valor` (ex.: `X-Api-Key:abc123`) |
| **Bearer Token** | `Authorization: Bearer {seu_token_estático}` (PAT / chave de API do servidor) |
| **OAuth (login do usuário)** | Fluxo OAuth 2.1 *authorization code* interativo, por usuário — ver abaixo |

### OAuth (login do usuário)

Para servidores que exigem que cada pessoa entre com a própria conta (GitHub, Linear, Notion remoto, Salesforce, etc.):

1. Cadastre o servidor com **Autenticação = OAuth** e **Protocolo = Auto** (ou MCP oficial).
2. Na tela de admin, clique em **Autorizar** para conectar a **sua** conta. Isso também libera a descoberta de ferramentas (a lista descoberta é compartilhada entre todos os usuários).
3. Cada usuário final conecta a própria conta em **Configurações → Integrações → Servidores MCP**. Enquanto não conectar, ao chamar uma ferramenta desse servidor a IA responde orientando a conectar primeiro.

Nos bastidores o SDK faz descoberta RFC 9728, **Dynamic Client Registration** (RFC 7591) e **PKCE** automaticamente. Os tokens ficam por (usuário, servidor), criptografados com AES-256-GCM, e o *refresh* é transparente. Só o grant *authorization code* + *refresh token* é suportado (não há client-credentials nem private-key JWT).

**Requisitos do servidor:** expor `/.well-known/oauth-protected-resource` (RFC 9728) e um *authorization server* com *metadata* (RFC 8414 / OIDC Discovery). O `redirect_uri` que a Arena usa é `https://SEU-DOMINIO/api/mcp-servers/oauth/callback`.

### OAuth com cliente pré-registrado (servidores sem DCR)

Alguns servidores **não aceitam** Dynamic Client Registration e exigem um app OAuth registrado manualmente. Nesse caso preencha estes campos extras no formulário do servidor (só aparecem com Autenticação = OAuth):

| Campo | Quando preencher |
|---|---|
| **OAuth Client ID** | Sempre que o servidor não suporta DCR — isso desliga o registro dinâmico |
| **OAuth Client Secret** | Só se o app for *confidential* (o servidor emitiu um secret) |
| **Scopes** | Quando o servidor exige scopes explícitos (separados por espaço) |

> ⚠️ Trocar o Client ID ou o Secret depois **invalida as conexões existentes** — os usuários precisam clicar **Conectar** de novo.

**Exemplo — Salesforce Hosted MCP:**

1. Na org Salesforce, crie um **External Client App** (Connected Apps não funcionam para MCP) com OAuth habilitado, scopes `mcp_api` e `refresh_token`, e *callback URL* `https://SEU-DOMINIO/api/mcp-servers/oauth/callback`.
2. Anote o **Consumer Key** (= Client ID) e o **Consumer Secret**.
3. Na Arena: **Novo Servidor** → URL do endpoint MCP da org, Protocolo `Auto`, Autenticação `OAuth`, e cole Client ID + Secret + Scopes `mcp_api refresh_token`.
4. Clique em **Autorizar** → login Salesforce → volta conectado. **Redescobrir** lista as ferramentas. Cada usuário conecta a própria conta em Integrações; o Salesforce aplica as permissões de cada um.

---

## 📋 Construir um Servidor MCP Padrão

Use um SDK oficial — não implemente o protocolo à mão. Exponha o transporte **Streamable HTTP**; o SDK cuida de `initialize`, sessões, SSE e paginação de `tools/list` por você.

### TypeScript (`@modelcontextprotocol/sdk`)

```javascript
import express from "express";
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StreamableHTTPServerTransport } from "@modelcontextprotocol/sdk/server/streamableHttp.js";
import { z } from "zod";

const mcp = new McpServer({ name: "meu-servidor", version: "1.0.0" });

mcp.registerTool(
  "consultar_estoque",
  {
    description: "Consulta o estoque de um produto pelo SKU",
    inputSchema: { sku: z.string().describe("Código SKU do produto") },
  },
  async ({ sku }) => {
    const unidades = await consultarEstoque(sku); // sua lógica
    return { content: [{ type: "text", text: `SKU ${sku}: ${unidades} unidades disponíveis.` }] };
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

mcp = FastMCP("meu-servidor")

@mcp.tool()
async def consultar_estoque(sku: str) -> str:
    """Consulta o estoque de um produto pelo SKU"""
    unidades = await consultar_estoque_db(sku)  # sua lógica
    return f"SKU {sku}: {unidades} unidades disponíveis."

if __name__ == "__main__":
    mcp.run(transport="streamable-http")  # expõe POST/GET em /mcp
```

Documentação completa: [modelcontextprotocol.io](https://modelcontextprotocol.io).

---

## 🕸️ Legado — Dialeto HTTP Simplificado da Arena

Antes do suporte ao protocolo oficial, a Arena IA falava um dialeto próprio. Ele continua aceito e é o que servidores no-code / baseados em webhook (ex.: Zapier, n8n) usam. Force-o com **Protocolo = HTTP simplificado (Arena)**.

| Fim | Requisição | Resposta 2xx |
|---|---|---|
| Descoberta | `GET {base}/tools` (fallback `POST {base}/tools/list` com corpo JSON-RPC) | `{ "tools": [ { "name", "description", "inputSchema": {…} } ] }` |
| Execução | `POST {base}/tools/call` com `{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"<tool>","arguments":{…}}}` | `{ "content": [ { "type": "text", "text": "<resultado>" } ] }` |
| Health | `GET {base}/health` (opcional) | qualquer 2xx ou 405 |

### Exemplo de `tools/list`

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "tools": [
      {
        "name": "consultar_estoque",
        "description": "Consulta o estoque de um produto pelo SKU",
        "inputSchema": {
          "type": "object",
          "properties": {
            "sku": { "type": "string", "description": "Código SKU do produto" }
          },
          "required": ["sku"]
        }
      }
    ]
  }
}
```

### Exemplo de `tools/call`

```json
{
  "jsonrpc": "2.0",
  "id": 2,
  "result": {
    "content": [
      { "type": "text", "text": "Estoque do SKU ABC123: 47 unidades disponíveis." }
    ]
  }
}
```

---

## 🔁 Redescoberta de Ferramentas

Quando o servidor MCP adiciona ou altera ferramentas, o administrador deve clicar em **Redescobrir** no painel — sem necessidade de reconfigurar a conexão nem reautorizar.

---

## ⚠️ Limites de Execução & Boas Práticas

- **Tempo-limite:** cada chamada de ferramenta tem teto de **30 segundos**. Uma ferramenta lenta deve retornar um link e servir o resultado à parte.
- **Rede:** o host precisa ser **público** — endereços internos (localhost, `10.x`, `192.168.x`, link-local) são bloqueados (proteção SSRF).
- **Agent loop:** ferramentas MCP ativam o loop multi-passo automaticamente, então ferramentas encadeadas (ex.: "liste, depois leia") funcionam numa única mensagem.
- Mantenha as `descriptions` das ferramentas claras e específicas — o modelo usa esse texto para decidir quando chamar, e o roteador da Arena só oferece a ferramenta quando a mensagem do usuário tem palavras que casam com nome/descrição.
- Retorne mensagens de erro descritivas no campo `content` para que o modelo possa comunicar falhas ao usuário.
- Use HTTPS em produção e implemente rate limiting no seu servidor.

---

## 🩺 Troubleshooting

| Sintoma | Causa provável |
|---|---|
| "Nenhuma ferramenta descoberta" | Servidor mudou as ferramentas → clique em **Redescobrir**; ou servidor OAuth sem ninguém autorizado → clique em **Autorizar** |
| Ferramenta descoberta mas o modelo nunca a usa | Nome/descrição pouco descritivos — o roteador da Arena só oferece a ferramenta quando a mensagem do usuário casa com nome/descrição |
| "Autorização expirada" no chat | *Refresh* de token falhou → o usuário reconecta em Configurações → Integrações |
| Fluxo OAuth volta com erro | `redirect_uri` não registrado no *authorization server*; relógio do servidor fora de sincronia; `iss` divergente |
| Timeout em ferramenta lenta | Teto de 30 s por chamada — retorne um link e sirva o resultado à parte |
| Erro de rede ao Testar Conexão | Host não é público (bloqueio SSRF) |
