# Exemplo: Consulta de CNPJ / CNPJ Lookup

**Tipo:** Ação Customizada | **API:** [BrasilAPI](https://brasilapi.com.br) | **Método:** GET | **Auth:** Nenhuma

---

### PT-BR

```json
[
  {
    "type": "function",
    "function": {
      "name": "consultar_cnpj",
      "description": "Consulta dados de uma empresa brasileira a partir do CNPJ. Use quando o usuário informar um CNPJ e quiser saber razão social, situação cadastral, endereço ou outros dados da empresa. Exemplo: {\"cnpj\": \"19131243000197\"}",
      "parameters": {
        "type": "object",
        "properties": {
          "cnpj": {
            "type": "string",
            "description": "CNPJ sem pontuação, apenas números. Exemplo: 19131243000197"
          }
        },
        "required": ["cnpj"]
      }
    },
    "_url": "https://brasilapi.com.br/api/cnpj/v1/",
    "_method": "GET",
    "_param_location": "query"
  }
]
```

**Exemplo de retorno:**
```json
{
  "cnpj": "19131243000197",
  "razao_social": "OPEN KNOWLEDGE BRASIL",
  "situacao_cadastral": "ATIVA",
  "municipio": "SÃO PAULO",
  "uf": "SP",
  "descricao_porte": "DEMAIS"
}
```

---

### EN

```json
[
  {
    "type": "function",
    "function": {
      "name": "lookup_cnpj",
      "description": "Looks up data for a Brazilian company from its CNPJ tax ID. Use when the user provides a CNPJ and wants to know the company name, registration status, address or other details. Example: {\"cnpj\": \"19131243000197\"}",
      "parameters": {
        "type": "object",
        "properties": {
          "cnpj": {
            "type": "string",
            "description": "CNPJ without punctuation, numbers only. Example: 19131243000197"
          }
        },
        "required": ["cnpj"]
      }
    },
    "_url": "https://brasilapi.com.br/api/cnpj/v1/",
    "_method": "GET",
    "_param_location": "query"
  }
]
```
