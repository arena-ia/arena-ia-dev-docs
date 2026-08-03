# Ações em Modelos Customizados

## O que é uma Ação Customizada?

Uma **Ação Customizada** é uma integração com API REST definida diretamente no modelo customizado da Arena IA via bloco JSON. Ela permite que o assistente chame endpoints externos durante a conversa, utilizando o mecanismo de **function calling**.

---

## 🔄 Fluxo de Comunicação

```
Usuário → Mensagem → Arena IA
Arena IA → detecta intenção → chama função
Arena IA → HTTP Request → API Externa
Arena IA ← HTTP Response ← API Externa
Arena IA → resposta enriquecida → Usuário
```

---

## ⚙️ Como Configurar

1. Acesse **Produtividade → Meus Modelos**
2. Selecione o modelo desejado
3. Na seção **Ações**, clique em **Avançado**
4. Cole o bloco JSON da ação
5. Salve o modelo

---

## 📋 Estrutura do JSON

```json
[
  {
    "type": "function",
    "function": {
      "name": "nome_da_funcao",
      "description": "Descrição clara do que a função faz e quando usá-la",
      "parameters": {
        "type": "object",
        "properties": {
          "parametro1": {
            "type": "string",
            "description": "Descrição do parâmetro"
          }
        },
        "required": ["parametro1"]
      }
    },
    "_url": "https://api.exemplo.com/endpoint",
    "_method": "GET",
    "_param_location": "query",
    "_headers": {
      "Authorization": "Bearer SEU_TOKEN"
    }
  }
]
```

---

## 🔧 Campos Disponíveis

| Campo | Obrigatório | Descrição |
|---|---|---|
| `type` | ✅ | Sempre `"function"` |
| `function.name` | ✅ | Nome único da função (sem espaços) |
| `function.description` | ✅ | Descrição usada pelo modelo para decidir quando chamar |
| `function.parameters` | ✅ | Schema JSON dos parâmetros (JSON Schema) |
| `_url` | ✅ | URL base do endpoint |
| `_method` | ✅ | `GET`, `POST`, `PUT`, `PATCH` ou `DELETE` |
| `_param_location` | ✅ | `query` (GET) ou `body` (POST/PUT) |
| `_headers` | ❌ | Headers HTTP adicionais (ex: autenticação) |

---

## 🔐 Autenticação

### Bearer Token
```json
"_headers": {
  "Authorization": "Bearer SEU_TOKEN_AQUI"
}
```

### API Key no Header
```json
"_headers": {
  "X-Api-Key": "SUA_CHAVE_AQUI"
}
```

### API Key na URL (query param)
Inclua diretamente na `_url`:
```json
"_url": "https://api.exemplo.com/dados?api_key=SUA_CHAVE"
```

---

## ⚠️ Boas Práticas

- Escreva `descriptions` detalhadas — elas guiam o modelo na decisão de usar a ação
- Use nomes de função descritivos: `consultar_pedido` é melhor que `api1`
- Marque como `required` apenas os campos realmente obrigatórios
- Prefira HTTPS em todos os endpoints
- Evite expor tokens sensíveis em modelos compartilhados
