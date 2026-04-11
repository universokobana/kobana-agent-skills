*[Read in English](README.md)*

> **Nota:** Este repositório é um fork do [repositório de skills da Anthropic](https://github.com/anthropics/skills), adaptado para a [Kobana](https://ai.kobana.com.br) - plataforma de automação financeira com IA nativa. Para informações sobre o padrão Agent Skills, veja [agentskills.io](http://agentskills.io).

# Skills

Skills são pastas com instruções, scripts e recursos que o Claude carrega dinamicamente para melhorar o desempenho em tarefas especializadas. Skills ensinam o Claude a completar tarefas específicas de forma repetível, seja criando documentos seguindo diretrizes da sua empresa, analisando dados usando workflows específicos da sua organização, ou automatizando tarefas pessoais.

Para mais informações:
- [What are skills?](https://support.claude.com/en/articles/12512176-what-are-skills)
- [Using skills in Claude](https://support.claude.com/en/articles/12512180-using-skills-in-claude)
- [How to create custom skills](https://support.claude.com/en/articles/12512198-creating-custom-skills)
- [Equipping agents for the real world with Agent Skills](https://anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)

# Sobre Este Repositório

Este repositório contém as skills oficiais da Kobana para automação financeira com Claude. Estas skills ajudam você a interagir com as APIs e servidores MCP da Kobana para cobranças Pix, pagamentos e operações financeiras.

Cada skill suporta dois modos: **MCP (preferido)** quando o servidor MCP correspondente está configurado, e **API REST (fallback)** quando não está.

## Sobre a Kobana

A [Kobana](https://www.kobana.com.br) é uma plataforma de automação financeira que conecta empresas a mais de 40 bancos brasileiros através de uma API unificada. Com mais de R$ 50 bilhões em transações processadas e 5+ milhões de operações bancárias mensais, a Kobana oferece soluções de recebimento, pagamento e transferência com IA nativa integrada.

## Skills Disponíveis

| Skill | Descrição |
|-------|-----------|
| [charge-pix](./skills/charge-pix) | Criar e gerenciar cobranças Pix, contas e pagamentos |
| [transfer-pix](./skills/transfer-pix) | Criar e gerenciar transferências Pix e lotes |
| [payment-pix](./skills/payment-pix) | Pagar cobranças Pix, decodificar QR codes e gerenciar lotes de pagamento |

## Configuração dos Servidores MCP

As skills utilizam os **servidores MCP remotos** hospedados pela Kobana. Cada endpoint em `https://mcp.kobana.com.br/{namespace}/mcp` implementa OAuth 2.1 + PKCE + Dynamic Client Registration, então o cliente negocia a autenticação no navegador — você nunca precisa colar client id, secret ou token estático.

### Endpoints remotos

| Namespace | URL |
|---|---|
| Admin     | `https://mcp.kobana.com.br/admin/mcp` |
| Charge    | `https://mcp.kobana.com.br/charge/mcp` |
| Data      | `https://mcp.kobana.com.br/data/mcp` |
| EDI       | `https://mcp.kobana.com.br/edi/mcp` |
| Financial | `https://mcp.kobana.com.br/financial/mcp` |
| Mailbox   | `https://mcp.kobana.com.br/mailbox/mcp` |
| Payment   | `https://mcp.kobana.com.br/payment/mcp` |
| Transfer  | `https://mcp.kobana.com.br/transfer/mcp` |

> Para o sandbox, troque `mcp.kobana.com.br` por `mcp-sandbox.kobana.com.br`.

### Claude Code

Quando você instala o plugin deste marketplace, os servidores MCP remotos já vêm pré-configurados automaticamente — não é preciso editar nenhum arquivo nem definir variável de ambiente. No primeiro uso de uma ferramenta, o Claude Code abre o navegador para você completar o login OAuth da Kobana.

Se preferir registrar os servidores manualmente, use o comando `claude mcp add` com `--transport http`:

```bash
claude mcp add --transport http kobana-financial https://mcp.kobana.com.br/financial/mcp
```

### Claude Desktop

Settings → **Connectors → Add Custom Connector**. Cole a URL do namespace desejado, deixe os campos avançados vazios, clique em **Add** e complete o login Kobana na janela do navegador que abrir. Repita para cada namespace que precisar.

Para instruções de setup em outros clientes (Claude.ai web, ChatGPT, Cursor, VS Code, Windsurf, Manus, Perplexity), veja o [README do kobana-mcp-servers](https://github.com/universokobana/kobana-mcp-servers#using-with-mcp-clients).

## Aviso Legal

**Estas skills são fornecidas para fins de demonstração e educacionais.** Sempre teste as skills completamente em seu próprio ambiente antes de depender delas para tarefas críticas.

# Estrutura do Repositório

- [./skills](./skills): Skills de automação financeira da Kobana
- [./spec](./spec): Especificação do Agent Skills
- [./template](./template): Template de skill

# Use no Claude Code, Claude.ai e na API

## Claude Code

Você pode registrar este repositório como um marketplace de plugins do Claude Code executando o seguinte comando:
```
/plugin marketplace add universokobana/kobana-agent-skills
```

Então, para instalar as skills da Kobana:
1. Selecione `Browse and install plugins`
2. Selecione `kobana-agent-skills`
3. Selecione `kobana-agent-skills`
4. Selecione `Install now`

Alternativamente, instale diretamente via:
```
/plugin install kobana-agent-skills@kobana-agent-skills
```

Após instalar o plugin, você pode usar a skill apenas mencionando-a. Por exemplo:
- "Use a skill charge-pix para criar uma cobrança Pix de R$ 100,00"
- "Use a skill charge-pix para listar minhas contas Pix"
- "Use a skill transfer-pix para enviar uma transferência Pix de R$ 500,00"
- "Use a skill transfer-pix para criar um lote de transferências"
- "Use a skill payment-pix para pagar um QR code Pix"
- "Use a skill payment-pix para decodificar e pagar uma fatura Pix"

## Claude.ai

Para usar qualquer skill deste repositório ou fazer upload de skills personalizadas, siga as instruções em [Using skills in Claude](https://support.claude.com/en/articles/12512180-using-skills-in-claude#h_a4222fa77b).

## Claude API

Você pode usar skills pré-construídas e fazer upload de skills personalizadas via Claude API. Veja o [Skills API Quickstart](https://docs.claude.com/en/api/skills-guide#creating-a-skill) para mais informações.

# Criando uma Skill Básica

Skills são simples de criar - apenas uma pasta com um arquivo `SKILL.md` contendo frontmatter YAML e instruções. Você pode usar o **template** neste repositório como ponto de partida:

> **Importante:** Ao criar ou remover skills, você deve atualizar os seguintes arquivos:
> - `.claude-plugin/marketplace.json` - Registrar/desregistrar a skill no array `plugins[0].skills`
> - `README.md` e `README.pt-BR.md` - Atualizar a tabela "Skills Disponíveis"
> - `CLAUDE.md` - Atualizar as seções de estrutura do repositório e skills disponíveis

```markdown
---
name: minha-skill
description: Uma descrição clara do que esta skill faz e quando usá-la
---

# Nome da Minha Skill

[Adicione suas instruções aqui que o Claude seguirá quando esta skill estiver ativa]

## Exemplos
- Exemplo de uso 1
- Exemplo de uso 2

## Diretrizes
- Diretriz 1
- Diretriz 2
```

O frontmatter requer apenas dois campos:
- `name` - Um identificador único para sua skill (minúsculas, hífens para espaços)
- `description` - Uma descrição completa do que a skill faz e quando usá-la

O conteúdo markdown contém as instruções, exemplos e diretrizes que o Claude seguirá. Para mais detalhes, veja [How to create custom skills](https://support.claude.com/en/articles/12512198-creating-custom-skills).

# Recursos Adicionais

- [Site da Kobana](https://www.kobana.com.br)
- [Kobana AI](https://ai.kobana.com.br)
- [Documentação da API Kobana](https://developers.kobana.com.br)
- [Servidores MCP da Kobana](https://github.com/universokobana/kobana-mcp-servers)
- [Especificações da API Kobana](https://github.com/universokobana/kobana-api-specs)
