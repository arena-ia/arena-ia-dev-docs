# Template: POST Genérico / Generic POST

**Tipo:** Ação Customizada | **Método:** POST | **Auth:** Nenhuma

---

### PT-BR

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
          "campo1": {
            "type": "string",
            "description": "Descrição do campo 1"
          },
          "campo2": {
            "type": "number",
            "description": "Descrição do campo 2 (numérico)"
          },
          "campo3": {
            "type": "boolean",
            "description": "Descrição do campo 3 (verdadeiro/falso)"
          }
        },
        "required": ["campo1"]
      }
    },
    "_url": "https://api.seuservico.com/endpoint",
    "_method": "POST",
    "_param_location": "body"
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
      "description": "Describe here what the function does and when the model should use it.",
      "parameters": {
        "type": "object",
        "properties": {
          "field1": {
            "type": "string",
            "description": "Description of field 1"
          },
          "field2": {
            "type": "number",
            "description": "Description of field 2 (numeric)"
          },
          "field3": {
            "type": "boolean",
            "description": "Description of field 3 (true/false)"
          }
        },
        "required": ["field1"]
      }
    },
    "_url": "https://api.yourservice.com/endpoint",
    "_method": "POST",
    "_param_location": "body"
  }
]
```

---

> 💡 **Dica / Tip:** para requisições POST, use `_param_location: "body"`. Os parâmetros serão enviados como JSON no corpo da requisição.
