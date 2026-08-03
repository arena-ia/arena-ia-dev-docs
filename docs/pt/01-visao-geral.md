# Visão Geral da Plataforma Arena IA

A plataforma Arena IA permite que empresas contratantes estendam as capacidades dos assistentes de IA por meio de integrações externas. Existem três abordagens principais para conectar serviços e APIs à plataforma: **Servidores MCP**, **Ações em Modelos Customizados** e **Chaves de API** (para a direção reversa — sistemas externos chamando a Arena IA).

---

## 🏗️ Arquitetura de Integração

```
┌─────────────────────────────────────┐
│              Arena IA                │
│                                       │
│  ┌─────────────┐  ┌──────────────┐  │
│  │Servidor MCP │  │Ação Customiz.│  │
│  └──────┬──────┘  └──────┬───────┘  │
│         │                │          │
└─────────┼────────────────┼──────────┘
          │                │
     MCP Protocol      REST HTTP
          │                │
   ┌──────▼──────┐  ┌──────▼───────┐
   │  Servidor   │  │     API      │
   │ MCP Externo │  │  REST Externa│
   └─────────────┘  └──────────────┘
```

## 🔑 Conceitos Fundamentais

### Function Calling

Ambas as abordagens de saída utilizam function calling — mecanismo pelo qual o modelo de linguagem decide, durante a conversa, quando e como chamar uma ferramenta externa. O resultado retornado pela API é incorporado à resposta da IA de forma transparente para o usuário.

> ⚠️ **Limites de execução:** cada chamada tem um tempo-limite de 15 segundos, e endereços de rede interna (fora dos serviços da própria plataforma) são bloqueados por padrão.

### Servidores MCP

- Baseado no Model Context Protocol (MCP), padrão aberto mantido pela Anthropic
- Configurado pelo administrador da plataforma
- Disponibiliza múltiplas ferramentas de uma só vez via descoberta automática
- Escopo: grupo de usuários ou global

### Ações em Modelos Customizados

- Baseadas em chamadas REST HTTP simples
- Configuradas pelo criador do modelo customizado
- Cada ação é definida manualmente via um bloco JSON
- Escopo: modelo específico onde foi configurada

### Chaves de API

- Direção reversa: dá a um sistema externo acesso de entrada à Arena IA
- Configurado pelo administrador da plataforma
- Usa o mesmo padrão de acesso de APIs de IA como OpenAI ou Anthropic
- Escopo: um ou mais grupos (a chave herda a união das permissões deles)

---

## 📊 Comparativo Rápido

A tabela abaixo compara **Servidores MCP** e **Ações Customizadas**, já que ambas são de saída (a Arena IA chamando um serviço externo). **Chaves de API** funcionam diferente — são a direção reversa, de entrada — então não entram nessa tabela; veja [Chaves de API](05-chaves-de-api.md) para detalhes.

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

- Seu serviço já expõe um endpoint MCP? → Servidores MCP
- Quer conectar uma API REST a um assistente específico? → Ações Customizadas
- Quer que um sistema externo chame a Arena IA diretamente? → Chaves de API
- Quer ver exemplos prontos? → pasta examples
