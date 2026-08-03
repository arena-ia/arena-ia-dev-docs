# Template: Autenticação Bearer / Bearer Auth Template

**Tipo:** Ação Customizada | **Método:** GET ou POST | **Auth:** Bearer Token

---

## PT-BR

Use este template como ponto de partida para qualquer integração que exija autenticação via Bearer Token.

```json
[
  {
    "type": "function",
    "function": {
      "name": "nome_da_funcao",
      "description": "Descreva aqui o que a função faz e quando o modelo deve usá-la.",
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
    "_url": "https://api.seuservico.com/endpoint-protegido",
    "_method": "GET",
    "_param_location": "query",
    "_headers": {
      "Authorization": "Bearer SEU_TOKEN_AQUI",
      "Content-Type": "application/json"
    }
  }
]
```

> ⚠️ **Atenção:** nunca exponha tokens reais em modelos compartilhados com outros usuários.

---

## EN

Use this template as a starting point for any integration that requires Bearer Token authentication.

```json
[
  {
    "type": "function",
    "function": {
      "name": "function_name",
      "description": "Describe here what the function does and when the model should use it.",
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
    "_url": "https://api.yourservice.com/protected-endpoint",
    "_method": "GET",
    "_param_location": "query",
    "_headers": {
      "Authorization": "Bearer YOUR_TOKEN_HERE",
      "Content-Type": "application/json"
    }
  }
]
```

> ⚠️ **Warning:** never expose real tokens in models shared with other users.
