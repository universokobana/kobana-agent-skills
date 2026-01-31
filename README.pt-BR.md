> **Nota:** Este repositório é um fork do [repositório de skills da Anthropic](https://github.com/anthropics/skills), adaptado para a [Kobana](https://ai.kobana.com.br) - plataforma de automação financeira com IA nativa. Para informações sobre o padrão Agent Skills, veja [agentskills.io](http://agentskills.io).

# Skills

Skills são pastas com instruções, scripts e recursos que o Claude carrega dinamicamente para melhorar o desempenho em tarefas especializadas. Skills ensinam o Claude a completar tarefas específicas de forma repetível, seja criando documentos seguindo diretrizes da sua empresa, analisando dados usando workflows específicos da sua organização, ou automatizando tarefas pessoais.

Para mais informações:
- [What are skills?](https://support.claude.com/en/articles/12512176-what-are-skills)
- [Using skills in Claude](https://support.claude.com/en/articles/12512180-using-skills-in-claude)
- [How to create custom skills](https://support.claude.com/en/articles/12512198-creating-custom-skills)
- [Equipping agents for the real world with Agent Skills](https://anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)

# Sobre Este Repositório

Este repositório contém skills que demonstram o que é possível com o sistema de skills do Claude. As skills variam desde aplicações criativas (arte, música, design) até tarefas técnicas (testes de web apps, geração de servidores MCP) e workflows empresariais (comunicações, branding, etc.).

Cada skill é auto-contida em sua própria pasta com um arquivo `SKILL.md` contendo as instruções e metadados que o Claude utiliza. Navegue por estas skills para se inspirar ou para entender diferentes padrões e abordagens.

## Sobre a Kobana

A [Kobana](https://ai.kobana.com.br) é uma plataforma de automação financeira que conecta empresas a mais de 40 bancos brasileiros através de uma API unificada. Com mais de R$ 50 bilhões em transações processadas e 5+ milhões de operações bancárias mensais, a Kobana oferece soluções de recebimento, pagamento e transferência com IA nativa integrada.

## Aviso Legal

**Estas skills são fornecidas para fins de demonstração e educacionais.** Embora algumas dessas capacidades possam estar disponíveis no Claude, as implementações e comportamentos podem diferir do que é mostrado nestas skills. Estas skills servem para ilustrar padrões e possibilidades. Sempre teste as skills completamente em seu próprio ambiente antes de depender delas para tarefas críticas.

# Conjuntos de Skills
- [./skills](./skills): Exemplos de skills para Criativo & Design, Desenvolvimento & Técnico, Empresarial & Comunicação, e Skills de Documentos
- [./spec](./spec): Especificação do Agent Skills
- [./template](./template): Template de skill

# Use no Claude Code, Claude.ai e na API

## Claude Code
Você pode registrar este repositório como um marketplace de plugins do Claude Code executando o seguinte comando:
```
/plugin marketplace add kobana/kobana-skills
```

Então, para instalar um conjunto específico de skills:
1. Selecione `Browse and install plugins`
2. Selecione `kobana-agent-skills`
3. Selecione `document-skills` ou `example-skills`
4. Selecione `Install now`

Alternativamente, instale diretamente via:
```
/plugin install document-skills@kobana-agent-skills
/plugin install example-skills@kobana-agent-skills
```

Após instalar o plugin, você pode usar a skill apenas mencionando-a. Por exemplo, se você instalar o plugin `document-skills` do marketplace, você pode pedir ao Claude Code algo como: "Use a skill de PDF para extrair os campos de formulário de `caminho/para/arquivo.pdf`"

## Claude.ai

Estas skills de exemplo já estão disponíveis para planos pagos no Claude.ai.

Para usar qualquer skill deste repositório ou fazer upload de skills personalizadas, siga as instruções em [Using skills in Claude](https://support.claude.com/en/articles/12512180-using-skills-in-claude#h_a4222fa77b).

## Claude API

Você pode usar skills pré-construídas e fazer upload de skills personalizadas via Claude API. Veja o [Skills API Quickstart](https://docs.claude.com/en/api/skills-guide#creating-a-skill) para mais informações.

# Criando uma Skill Básica

Skills são simples de criar - apenas uma pasta com um arquivo `SKILL.md` contendo frontmatter YAML e instruções. Você pode usar o **template-skill** neste repositório como ponto de partida:

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

O conteúdo markdown abaixo contém as instruções, exemplos e diretrizes que o Claude seguirá. Para mais detalhes, veja [Como criar skills customizadas (en)](https://support.claude.com/en/articles/12512198-creating-custom-skills).