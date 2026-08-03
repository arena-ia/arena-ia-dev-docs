# Chaves de API

## O que é uma Chave de API?

Diferente de um Servidor MCP ou Custom Action — que dão à Aria acesso de **saída** a um sistema externo —, uma Chave de API é o caminho inverso: dá a um **sistema externo** acesso de **entrada** a uma instância da Arena IA, sem passar por um usuário logado no navegador. É o mesmo tipo de acesso que APIs de IA como OpenAI ou Anthropic oferecem.

Use quando quiser que um chatbot em outra plataforma, um script, ou uma automação (ex: n8n) converse diretamente com a Arena IA.

## ⚙️ Como Criar

1. Acesse **Administração → Chaves de API**
2. Clique em **Nova Chave**
3. Dê um nome descritivo (ex: `Integração Typebot`)
4. Selecione um ou mais **grupos** aos quais a chave pertence — a chave herda a **união** dos modelos e integrações permitidos por todos os grupos selecionados (não é possível conceder a uma chave mais acesso do que a soma desses grupos)
5. Confirme a criação

## 🔑 Valor da Chave

O valor completo (`arena-sk-...`) é exibido **uma única vez**, na tela de criação, junto com um exemplo de chamada pronto para copiar (`curl`).

> ⚠️ **Guarde a chave em local seguro assim que criá-la.** Ela não pode ser recuperada depois — apenas revogada e substituída por uma nova.

## 📡 Como Chamar

Envie a chave no cabeçalho `Authorization`, no mesmo formato usado por APIs de IA como a da OpenAI:

```
Authorization: Bearer arena-sk-SEU_VALOR_AQUI
```

O corpo da requisição segue o padrão de chat completions (`messages`, `model`, `stream`). Consulte o exemplo de `curl` exibido na tela de criação da chave na sua instância para o endpoint exato e o formato completo do payload.

## 🛠️ Gerenciamento

- **Editar**: renomeie a chave ou ajuste quais grupos ela usa a qualquer momento — o valor secreto em si nunca é reexibido nem editável, só o nome e os grupos vinculados.
- **Revogar**: desativa o acesso imediatamente e não pode ser reativada — crie uma nova chave se precisar.

## ⚠️ Boas Práticas

- Nunca exponha o valor real da chave em repositórios públicos, logs ou modelos compartilhados
- Crie uma chave por integração/sistema — facilita revogar uma sem afetar as demais
- Revogue chaves de integrações descontinuadas

- Crie uma chave por integração/sistema — facilita revogar uma sem afetar as demais
- Revogue chaves de integrações descontinuadas
