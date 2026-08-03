# Visão Geral da Plataforma Arena IA

A plataforma Arena IA permite que empresas contratantes estendam as capacidades dos assistentes de IA por meio de integrações externas. Existem duas abordagens principais para conectar serviços e APIs à plataforma: **Servidores MCP** e **Ações em Modelos Customizados**.

---

## 🏗️ Arquitetura de Integração

```
┌─────────────────────────────────────┐
│            Arena IA                 │
│                                     │
│  ┌─────────────┐  ┌──────────────┐  │
│  │ Servidor MCP│  │Ação Customiz.│  │
│  └──────┬──────┘  └──────┬───────┘  │
│         │                │          │
└─────────┼────────────────┼──────────┘
          │                │
     MCP Protocol       REST HTTP
          │                │
   ┌──────▼──────┐  ┌──────▼───────┐
   │ Servidor MCP│  │   API REST   │
   │   Externo   │  │   Externa    │
   └─────────────┘  └──────────────┘
```

---

## 🔑 Conceitos Fundamentais

### Function Calling
Ambas as abordagens utilizam **function calling** — mecanismo pelo qual o modelo de linguagem decide, durante a conversa, quando e como chamar uma ferramenta externa. O resultado retornado pela API é incorporado à resposta da IA de forma transparente para o usuário.

### Servidores MCP
- Baseados no protocolo **Model Context Protocol (MCP)**, padrão aberto mantido pela Anthropic
- Configurados pelo **administrador** da plataforma
- Disponibilizam múltiplas ferramentas de uma só vez via descoberta automática
- Escopo: **grupo de usuários** ou **global**

### Ações em Modelos Customizados
- Baseadas em chamadas **REST HTTP** simples
- Configuradas pelo **criador do modelo customizado**
- Cada ação é definida manualmente via bloco JSON
- Escopo: **modelo específico** onde foi configurada

---

## 📊 Comparativo Rápido

| Critério | Servidor MCP | Ação Customizada |
|---|---|---|
| Protocolo | MCP (JSON-RPC 2.0) | REST HTTP |
| Configurado por | Administrador | Criador do modelo |
| Escopo | Grupo / Global | Modelo específico |
| Descoberta de ferramentas | Automática | Manual (JSON) |
| Múltiplas ferramentas | Sim | Uma por bloco |
| Ideal para | Plataformas MCP-compatíveis | APIs pontuais |

---

## 🗺️ Por onde começar?

- Seu serviço já expõe um endpoint MCP? → [Servidores MCP](02-servidores-mcp.md)
- Quer conectar uma API REST a um assistente específico? → [Ações Customizadas](03-acoes-customizadas.md)
- Quer ver exemplos prontos? → [pasta examples](../../examples/)
