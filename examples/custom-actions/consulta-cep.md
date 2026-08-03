# Exemplo: Consulta de CEP / CEP Lookup

**Tipo:** Ação Customizada | **API:** [BrasilAPI](https://brasilapi.com.br) | **Método:** GET | **Auth:** Nenhuma

---

### PT-BR

```json
[
  {
    "type": "function",
    "function": {
      "name": "consultar_cep",
      "description": "Consulta o endereço completo a partir de um CEP brasileiro. Use quando o usuário informar um CEP e quiser saber o endereço. Exemplo de entrada: {\"cep\": \"01310100\"}",
      "parameters": {
        "type": "object",
        "properties": {
          "cep": {
            "type": "string",
            "description": "CEP sem hífen e sem espaços. Exemplo: 01310100"
          }
        },
        "required": ["cep"]
      }
    },
    "_url": "https://brasilapi.com.br/api/cep/v1/",
    "_method": "GET",
    "_param_location": "query"
  }
]
```

**Exemplo de retorno:**
```json
{
  "cep": "01310100",
  "state": "SP",
  "city": "São Paulo",
  "neighborhood": "Bela Vista",
  "street": "Avenida Paulista",
  "service": "open-cep"
}
```

---

### EN

```json
[
  {
    "type": "function",
    "function": {
      "name": "lookup_zip_code",
      "description": "Looks up the full address from a Brazilian zip code (CEP). Use when the user provides a CEP and wants to know the address. Example input: {\"cep\": \"01310100\"}",
      "parameters": {
        "type": "object",
        "properties": {
          "cep": {
            "type": "string",
            "description": "CEP without hyphen or spaces. Example: 01310100"
          }
        },
        "required": ["cep"]
      }
    },
    "_url": "https://brasilapi.com.br/api/cep/v1/",
    "_method": "GET",
    "_param_location": "query"
  }
]
```
