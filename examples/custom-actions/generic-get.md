# Template: GET Genérico / Generic GET

**Tipo:** Ação Customizada | **Método:** GET | **Auth:** Nenhuma

Use este template como ponto de partida para qualquer integração GET sem autenticação.

---

### PT-BR

```json
[
  {
    "type": "function",
    "function": {
      "name": "nome_da_funcao",
      "description": "Descreva aqui o que a função faz e quando o modelo deve usá-la. Seja específico para evitar chamadas desnecessárias.",
      "parameters": {
        "type": "object",
        "properties": {
          "parametro1": {
            "type": "string",
            "description": "Descrição do primeiro parâmetro"
          },
          "parametro2": {
            "type": "string",
            "description": "Descrição do segundo parâmetro (opcional)"
          }
        },
        "required": ["parametro1"]
      }
    },
    "_url": "https://api.seuservico.com/endpoint",
    "_method": "GET",
    "_param_location": "query"
  }
]
```

---

### EN

```json
[
  {
    "type": "function",
    "function": {
      "name": "function_name",
      "description": "Describe here what the function does and when the model should use it. Be specific to avoid unnecessary calls.",
      "parameters": {
        "type": "object",
        "properties": {
          "parameter1": {
            "type": "string",
            "description": "Description of the first parameter"
          },
          "parameter2": {
            "type": "string",
            "description": "Description of the second parameter (optional)"
          }
        },
        "required": ["parameter1"]
      }
    },
    "_url": "https://api.yourservice.com/endpoint",
    "_method": "GET",
    "_param_location": "query"
  }
]
```

---

> 💡 **Dica / Tip:** substitua `nome_da_funcao` e `parametro1` por nomes descritivos relacionados ao seu domínio. O modelo de IA usa esses nomes e descrições para decidir quando e como chamar a função.
