# Resumo de Conceitos de IA para Iniciantes

> Guia completo para entender **LLMs, Models, Agents e MCP** — com opções gratuitas e links para começar hoje.

## 1. O que é uma LLM?

**LLM** significa **Large Language Model** (Modelo de Linguagem de Grande Porte). É o tipo de tecnologia que está por trás do ChatGPT, Claude, Gemini, etc.

### O que significa cada palavra

| **Letra** | **Termo** | **Significado** |
|-----------|-----------|-----------------|
| **L** | Language | Linguagem — entende e gera texto em linguagem humana |
| **L** | Large | Grande — bilhões de parâmetros, treinado com uma quantidade gigantesca de dados (livros, artigos, internet) |
| **M** | Model | Modelo — o "cérebro" matemático que aprendeu padrões estatísticos |

### Como funciona (simplificado)

Você digita: "Qual a capital da França?"

O LLM faz:

1. Divide sua frase em pedaços (**tokens**)
2. Calcula, com probabilidade, qual palavra vem depois
3. Gera: *"A capital da França é Paris."* — porque aprendeu esse padrão em milhões de textos

### Analogia simples

Imagine uma pessoa que leu **todos os livros, artigos e sites do mundo**. Ela não "entende" de verdade — mas aprendeu que certas palavras aparecem juntas com muita frequência. Quando você pergunta, ela monta a resposta baseada nesses padrões estatísticos.

### Curiosidade

- O **"G"** no GPT significa **Generative** (Generativo) — gera texto novo, não apenas copia
- O **"T"** significa **Transformer** — a arquitetura inventada em 2017 que revolucionou tudo
- O artigo original se chama *"Attention Is All You Need"*

---

## 2. O que é um Model (Modelo)?

Um **model** é o "cérebro" da IA em si. É um programa treinado com enormes quantidades de texto para aprender padrões de linguagem.

- **O que faz:** recebe uma pergunta e gera uma resposta
- **Exemplos:** GPT-4, Claude Sonnet, Gemini 2.5 Flash
- **Analogia:** é como uma enciclopédia gigante que conversa com você — mas que às vezes "inventa" coisas (o que chamamos de **alucinação**)

### Diferença entre LLM e Model

Na prática, os termos são usados de forma intercambiável. **LLM** é a **categoria** (modelo de linguagem grande); **Model** é a **instância específica** (Claude Sonnet, GPT-4, etc.).

---

## 3. O que é um Agent (Agente)?

Um **agente** é o modelo + a capacidade de **agir** no mundo real.

Enquanto o modelo só responde perguntas, o agente pode:

- Ler e editar arquivos
- Rodar comandos no computador
- Buscar na internet
- Usar ferramentas externas
- Tomar decisões autônomas

### Analogia

| **Conceito** | **Analogia** |
|--------------|--------------|
| **Modelo** | Um consultor inteligente que só aconselha |
| **Agente** | Um funcionário que não só aconselha, mas **executa** tarefas |

### Componentes de um agente

```
┌─────────────────────────────────────┐
│             AGENTE                  │
│  ┌───────────────┐  ┌──────────────┐│
│  │    MODELO     │  │  FERRAMENTAS ││
│  │  (cérebro)    │  │   (ações)    ││
│  │  Claude/GPT   │  │  MCP tools   ││
│  └───────────────┘  └──────────────┘│
└─────────────────────────────────────┘
```

> No **OpenCode**, os agentes são definidos em `.opencode/agent/` e cada um tem seu papel (programador, planejador, explorador).

---

## 4. O que é MCP (Model Context Protocol)?

**MCP** é um **protocolo padronizado** para conectar modelos de IA a ferramentas externas. Criado pela **Anthropic em 2024**, tornou-se o padrão da indústria em 2026.

### Analogia perfeita: "USB para IA"

- **Antes do MCP:** cada ferramenta precisava de integração customizada (caótico)
- **Com MCP:** qualquer ferramenta que fale o "idioma MCP" conecta automaticamente

### Como funciona

```
┌──────────────────┐
│   SEU AGENTE     │
│ (Claude, GPT)    │
└────────┬─────────┘
         │
    ┌────┴────┐
    │   MCP   │  ← protocolo padrão (como USB)
    └────┬────┘
         │
    ┌────┴────────────────────────┐
    │    │    │    │    │    │    │
   PDF  Git  Web  DB  Slack Email  ← ferramentas reais
```

### O que MCP permite

- Agentes podem acessar Google Calendar, Notion, GitHub, etc.
- Claude Code pode gerar um site inteiro a partir de um design no Figma
- Chatbots corporativos podem conectar-se a múltiplos bancos de dados

### Vantagens

- **Padronizado:** funciona em Claude, Cursor, VS Code, Windsurf e outros
- **Plug and play:** qualquer servidor MCP funciona em qualquer cliente compatível
- **Comunidade:** +10.000 servidores MCP disponíveis em 2026

---

## 5. Diagrama Conceitual Completo

```
┌─────────────────────────────────────────────────┐
│                    LLM                          │
│        (Large Language Model)                   │
│      Modelo de Linguagem Grande                 │
│                                                 │
│  ┌───────────────────────────────────────────┐  │
│  │                MODEL                      │  │
│  │      Claude, GPT-4, Gemini, etc.          │  │
│  │           (o cérebro puro)                │  │
│  └───────────────────────────────────────────┘  │
│                     +                           │
│  ┌───────────────────────────────────────────┐  │
│  │                AGENT                      │  │
│  │   Capacidade de agir no mundo             │  │
│  │   (ler, escrever, buscar, executar)       │  │
│  └───────────────────────────────────────────┘  │
│                     ↕                            │
│  ┌───────────────────────────────────────────┐  │
│  │                MCP                        │  │
│  │   Protocolo para conectar ferramentas     │  │
│  │   (PDF, GitHub, Browser, DB, etc.)        │  │
│  └───────────────────────────────────────────┘  │
└─────────────────────────────────────────────────┘
```

**Resumo:**

- **LLM** = tipo de tecnologia
- **Model** = implementação específica
- **Agent** = modelo + ação
- **MCP** = protocolo de conexão

---

## 6. Melhores Opções Gratuitas (Agosto 2026)

### APIs gratuitas de LLM

| **Provedor** | **Modelo(s)** | **Limite Gratuito** | **Cartão?** | **Melhor para** | **Link para API Key** |
|--------------|---------------|---------------------|-------------|-----------------|------------------------|
| **Google Gemini** | Gemini 2.5 Flash, Pro | 1.500 req/dia | Não | Frontline gratuito mais generoso | [ai.google.dev](https://ai.google.dev) |
| **Groq** | Llama, Qwen, DeepSeek | 14.400 req/dia | Não | Velocidade absurda | [console.groq.com](https://console.groq.com) |
| **Cerebras** | Llama variants | Trial limitado | Não | Contexto longo, RAG | [inference-docs.cerebras.ai](https://inference-docs.cerebras.ai) |
| **OpenRouter** | 28+ modelos free | 1.000 req/dia | Não | Variedade — um só endpoint | [openrouter.ai](https://openrouter.ai) |
| **Mistral** | Mistral Small/Large | Tier gratuito | Não | Coding, modelos europeus | [console.mistral.ai](https://console.mistral.ai) |
| **GitHub Models** | GPT-4, Llama, etc | Limitado | Não | Acesso a modelos OpenAI grátis | [github.com/marketplace/models](https://github.com/marketplace/models) |
| **DeepSeek** | DeepSeek V4 | Créditos iniciais | Sim (grátis) | Coding open-source de ponta | [platform.deepseek.com](https://platform.deepseek.com) |
| **HuggingFace** | Milhares open-source | Rate-limited | Não | Experimentar modelos open | [huggingface.co](https://huggingface.co) |

### Melhores modelos open-source (para rodar local ou API barata)

| **Modelo** | **Licença** | **Melhor para** | **Contexto** |
|------------|-------------|-----------------|--------------|
| **DeepSeek V4** | MIT | Coding + agentic (empata com frontlines pagos) | 1M tokens |
| **Qwen 3.5** | Apache-2.0 | Versátil, todos os tamanhos | Variável |
| **Llama 4 Scout** | Meta Community | Maior ecossistema, fino-tuning | 10M tokens |
| **GLM-5.2** | MIT | Coding autônomo, engenharia | 1M tokens |
| **Kimi K2.5** | Apache-2.0 | Raciocínio profundo, análise | Grande |
| **Mistral Large 3** | Apache-2.0 | Eficiente, soberania europeia | Variável |

### Melhores MCP servers gratuitos

| **MCP Server** | **O que faz** | **Custo** | **GitHub** |
|----------------|---------------|-----------|------------|
| **Filesystem** | Ler/escrever arquivos | 100% local | [modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers) |
| **GitHub MCP** | Issues, PRs, code search | Gratuito com token | [github/github-mcp-server](https://github.com/github/github-mcp-server) |
| **Playwright** | Automação de navegador | Gratuito | [@anthropic-ai/mcp-playwright](https://www.npmjs.com/package/@anthropic-ai/mcp-playwright) |
| **Context7** | Docs atualizados para LLMs | Gratuito | [upstash/context7](https://github.com/upstash/context7) |
| **Supabase** | Banco de dados PostgreSQL | Tier gratuito | [@supabase/mcp-server](https://www.npmjs.com/package/@supabase/mcp-server) |
| **Sentry** | Monitoramento de erros | Tier gratuito | [@sentry/mcp-server](https://www.npmjs.com/package/@sentry/mcp-server) |
| **PDF Reader** | Ler e extrair texto de PDFs | 100% local | (criado localmente) |

---

## 7. Estratégia "Zero Custo" Recomendada

Para quem quer começar sem gastar nada:

1. **Gemini 2.5 Flash** → modelo principal (1.500 req/dia grátis)
2. **Groq** → respostas rápidas (14.400 req/dia grátis)
3. **OpenRouter** → backup quando um provedor cair
4. **MCP servers** → ferramentas externas (GitHub, PDF, browser)
5. **DeepSeek V4 local** → se tiver GPU (código ilimitado, sem limite)

### Matemática do custo total

```
Gemini Flash:     1.500 req/dia × 30 dias =  45.000 req/mês  = $0
Groq Llama:      14.400 req/dia × 30 dias = 432.000 req/mês  = $0
OpenRouter free:  1.000 req/dia × 30 dias =  30.000 req/mês  = $0
────────────────────────────────────────────────────────────────
TOTAL:                    ~507.000 req/mês = $0 (zero custo)
```

---

## 8. Cuidados Importantes

1. **Dados de treino:** tiers gratuitos geralmente usam seus prompts para treinar — nunca envie dados sensíveis
2. **Rate limits:** mude de provedor quando um bloquear
3. **Local é rei:** se precisar de privacidade total e volume ilimitado, rode Llama/Qwen via **Ollama**
4. **"Grátis" não significa "sem custo":** rodar modelos localmente precisa de GPU e eletricidade

---

## 9. Como Configurar no OpenCode

Exemplo de configuração `opencode.jsonc` com provedores gratuitos:

```jsonc
{
  "$schema": "https://opencode.ai/config.json",

  "model": "google/gemini-2.5-flash",
  "small_model": "groq/llama-4-scout",

  "provider": {
    "google": {
      "options": {
        "apiKey": "SUA_CHAVE_GEMINI_AQUI"
      }
    },
    "groq": {
      "options": {
        "apiKey": "SUA_CHAVE_GROQ_AQUI"
      }
    }
  },

  "mcp": {
    "pdf-reader": {
      "type": "local",
      "command": ["python3", "/home/harouca/.config/opencode/tools/pdf-reader/server.py"],
      "enabled": true
    },
    "github": {
      "type": "remote",
      "url": "https://api.github.com",
      "enabled": false
    }
  },

  "permission": {
    "bash": {
      "git *": "allow",
      "*": "ask"
    }
  },

  "skills": {
    "paths": [".opencode/skills"]
  }
}
```

---

## 10. Glossário Rápido

| **Termo** | **Definição** |
|-----------|----------------|
| **LLM** | Large Language Model — modelo de linguagem de grande porte |
| **Model** | Implementação específica de um LLM (ex: Claude Sonnet) |
| **Agent** | Modelo + capacidade de usar ferramentas e agir |
| **MCP** | Model Context Protocol — protocolo para conectar IA a ferramentas |
| **Token** | Pedacinho de texto — o LLM processa tokens, não palavras inteiras |
| **Context Window** | Quantidade de tokens que o modelo "enxerga" de uma vez |
| **Fine-tuning** | Treinar um modelo existente com dados específicos |
| **RAG** | Retrieval-Augmented Generation — buscar informações externas antes de responder |
| **API Key** | Chave de acesso para usar um serviço de IA via programação |
| **Rate Limit** | Limite de quantas requisições você pode fazer por minuto/dia |
| **Open Source / Open Weights** | Modelo cujos pesos treinados são downloadáveis |

---

*Documento gerado em agosto de 2026 para fins de estudo e consulta.*