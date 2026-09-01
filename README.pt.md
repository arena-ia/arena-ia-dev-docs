# arena-ia-dev-docs

> Documentação oficial para desenvolvedores que integram com a plataforma Arena IA.

Este repositório contém referências técnicas, guias e exemplos práticos para desenvolvedores das empresas contratantes que desejam integrar com a plataforma Arena IA via **Servidores MCP** ou **Ações Customizadas**.

---

## 📚 Documentação

| Seção | Português | English |
|---|---|---|
| Visão Geral da Plataforma | [docs/pt/01-visao-geral.md](docs/pt/01-visao-geral.md) | [docs/en/01-overview.md](docs/en/01-overview.md) |
| Servidores MCP | [docs/pt/02-servidores-mcp.md](docs/pt/02-servidores-mcp.md) | [docs/en/02-mcp-servers.md](docs/en/02-mcp-servers.md) |
| Ações Customizadas | [docs/pt/03-acoes-customizadas.md](docs/pt/03-acoes-customizadas.md) | [docs/en/03-custom-actions.md](docs/en/03-custom-actions.md) |
| Guia de Changelog | [docs/pt/04-guia-changelog.md](docs/pt/04-guia-changelog.md) | [docs/en/04-changelog-guide.md](docs/en/04-changelog-guide.md) |
| Chaves de API | [docs/pt/05-chaves-de-api.md](docs/pt/05-chaves-de-api.md) | [docs/en/05-api-keys.md](docs/en/05-api-keys.md) |

---

## ⚡ Quick Start

**Opção 1 — Servidor MCP**
Informe a URL do seu servidor MCP no painel de administração da Arena IA e deixe a plataforma descobrir suas ferramentas automaticamente.

**Opção 2 — Ação Customizada**
Defina um bloco JSON de ação diretamente no seu modelo customizado e conecte a qualquer API REST.

---

## 💡 Exemplos

| Exemplo | Tipo | Descrição |
|---|---|---|
| [Servidor MCP Oficial (Streamable HTTP)](examples/mcp-servers/official-streamable-http.md) | MCP | Recomendado — transporte oficial via SDK |
| [Servidor MCP Genérico](examples/mcp-servers/generic-mcp-server.md) | MCP | Configuração mínima usando o dialeto HTTP simplificado |
| [Zapier MCP](examples/mcp-servers/zapier-mcp.md) | MCP | Conexão via Zapier MCP |
| [Consulta de CEP](examples/custom-actions/consulta-cep.md) | Ação Customizada | Busca endereço por CEP |
| [Consulta de CNPJ](examples/custom-actions/consulta-cnpj.md) | Ação Customizada | Busca dados de empresa por CNPJ |
| [GET Genérico](examples/custom-actions/generic-get.md) | Ação Customizada | Template para qualquer requisição GET |
| [POST Genérico](examples/custom-actions/generic-post.md) | Ação Customizada | Template para qualquer requisição POST |
| [Auth Bearer](examples/custom-actions/generic-auth-bearer.md) | Ação Customizada | Template com autenticação Bearer |

---

## 🤝 Contribuindo

Encontrou um problema ou quer sugerir uma melhoria? Abra uma [Issue](../../issues) ou envie um Pull Request.
 Veja [CONTRIBUTING.md](CONTRIBUTING.md) para as diretrizes.

 ---

 ## 📮 Suporte

 Para suporte da plataforma (não relacionado a este repositório de documentação), entre em contato: suporte@arena-ia.com.
 
