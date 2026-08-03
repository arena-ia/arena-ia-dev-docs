# Servidores MCP

## O que é um Servidor MCP?

Um **Servidor MCP** é um serviço externo que implementa o protocolo **Model Context Protocol (MCP)** — padrão aberto baseado em JSON-RPC 2.0 que define como ferramentas são anunciadas e chamadas por modelos de linguagem.

Ao registrar um servidor MCP na Arena IA, a plataforma realiza a **descoberta automática** de todas as ferramentas expostas, tornando-as disponíveis para os usuários do grupo configurado.

---

## 🔄 Fluxo de Comunicação

```
Arena IA → POST /mcp (tools/list)   → Servidor MCP
Arena IA ← { tools: [...] }         ← Servidor MCP

[usuário faz pergunta]

Arena IA → POST /mcp (tools/call)   → Servidor MCP
          { name, arguments }
Arena IA ← { content: [...] }       ← Servidor MCP
```

---

## ⚙️ Como Registrar

1. Acesse **Administração → Servidores MCP**
2. Clique em **Novo Servidor**
3. Preencha os campos:
   - **Nome:** identificador amigável
   - **URL:** endpoint do servidor MCP
   - **Autenticação:** Nenhuma, API Key ou Bearer Token
4. Clique em **Testar Conexão**
5. Confirme as ferramentas descobertas
6. Defina o **escopo** (grupo ou global) e ative

---

## 🔐 Autenticação Suportada

| Tipo | Header enviado |
|---|---|
| Nenhuma | — |
| API Key | `X-Api-Key: {sua_chave}` |
| Bearer Token | `Authorization: Bearer {seu_token}` |

---

## 📋 Requisitos do Servidor MCP

Seu servidor deve implementar ao menos dois métodos:

### `tools/list`
Retorna a lista de ferramentas disponíveis:

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
            "sku": {
              "type": "string",
              "description": "Código SKU do produto"
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
Executa uma ferramenta e retorna o resultado:

```json
{
  "jsonrpc": "2.0",
  "id": 2,
  "result": {
    "content": [
      {
        "type": "text",
        "text": "Estoque do SKU ABC123: 47 unidades disponíveis."
      }
    ]
  }
}
```

---

## 🔁 Redescoberta de Ferramentas

Quando o servidor MCP adicionar novas ferramentas, o administrador deve clicar em **Redescobrir** no painel para que a plataforma atualize a lista — sem necessidade de reconfigurar a conexão.

---

## ⚠️ Boas Práticas

- Mantenha as `descriptions` das ferramentas claras e objetivas — o modelo usa esse texto para decidir quando chamar a ferramenta
- Retorne mensagens de erro descritivas no campo `content` para que o modelo possa comunicar falhas ao usuário
- Use HTTPS em produção
- Implemente rate limiting no seu servidor
