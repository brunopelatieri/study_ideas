# 🧠 Guia Definitivo de LLMs 2026: Qual Modelo Usar para JavaScript, Python, n8n, Automações, Agentes IA, LangChain, Supabase, Redis e Prompt Engineering

> **Guia baseado em benchmarks reais** — Comparativo completo entre Claude Opus/Sonnet, GPT-5/Codex, Gemini e DeepSeek para escolher o melhor LLM em cada contexto: código, automação n8n, agentes IA, OCR, engenharia de prompts e arquitetura de dados.

---

[![Last Updated](https://img.shields.io/badge/Atualizado-Maio%202026-blue?style=flat-square)](.)
[![Models](https://img.shields.io/badge/Modelos-8%20LLMs-green?style=flat-square)](.)
[![Contexts](https://img.shields.io/badge/Contextos-12%20Casos%20de%20Uso-orange?style=flat-square)](.)
[![License](https://img.shields.io/badge/Licen%C3%A7a-MIT-lightgrey?style=flat-square)](.)

---

## 📋 Sumário

- [Por que este guia existe](#-por-que-este-guia-existe)
- [Os Modelos Analisados](#-os-modelos-analisados)
- [Tabela de Benchmarks Gerais](#-tabela-de-benchmarks-gerais)
- [Comparativo por Caso de Uso](#-comparativo-por-caso-de-uso)
  - [JavaScript — Node Code n8n](#1-javascript--node-code-n8n)
  - [Python — Scripts e Automações](#2-python--scripts-e-automações)
  - [Fluxos n8n — JSON, Nodes e Workflows](#3-fluxos-n8n--json-nodes-e-workflows)
  - [LangChain, LangGraph e LangSmith](#4-langchain-langgraph-e-langsmith)
  - [Agentes IA — Conversacionais](#5-agentes-ia--conversacionais)
  - [Engenharia de Prompts para Agentes](#6-engenharia-de-prompts-para-agentes)
  - [OCR e Extração Estruturada de Dados](#7-ocr-e-extração-estruturada-de-dados)
  - [Estrutura de Dados — Redis, Postgres, Supabase](#8-estrutura-de-dados--redis-postgres-supabase)
  - [Arquitetura e Refatoração de Código](#9-arquitetura-e-refatoração-de-código)
  - [Geração de JSON e Schemas](#10-geração-de-json-e-schemas)
  - [Debug e Resolução de Erros](#11-debug-e-resolução-de-erros)
  - [Multimodal — Imagem, Vídeo e Documentos](#12-multimodal--imagem-vídeo-e-documentos)
- [Matriz de Decisão Rápida](#-matriz-de-decisão-rápida)
- [Guia de Escolha por Perfil](#-guia-de-escolha-por-perfil)
- [Custo-Benefício e Velocidade](#-custo-benefício-e-velocidade)
- [Stack Recomendada por Projeto](#-stack-recomendada-por-projeto)
- [Fontes e Referências](#-fontes-e-referências)

---

## 🎯 Por que este guia existe

Com a explosão de modelos LLM em 2026, a pergunta **"qual modelo usar para cada tarefa?"** tornou-se estratégica para times de engenharia, desenvolvedores solo e criadores de automações. Usar o modelo errado significa:

- 💸 **Custo desnecessário** — pagar pelo modelo mais caro quando um intermediário resolve
- 🐌 **Latência alta** — usar modelos de raciocínio pesado para tarefas simples
- ❌ **Qualidade inferior** — usar modelos rápidos para tarefas que exigem profundidade
- 🔁 **Retrabalho** — prompts mal estruturados por falta do modelo certo para engenharia de prompts

Este guia resolve isso com dados de benchmark e recomendações práticas para os contextos mais comuns de desenvolvimento e automação.

---

## 🤖 Os Modelos Analisados

### Anthropic — Claude 4.x

| Modelo | Tier | Foco Principal | Janela de Contexto |
|--------|------|---------------|-------------------|
| **Claude Opus 4.7** | 🥇 Premium | Raciocínio profundo, agentes complexos, prompt engineering | 200K tokens |
| **Claude Sonnet 4.6** | 🥈 Balanced | Velocidade + qualidade, coding, iteração rápida | 200K tokens |
| **Claude Haiku 4.5** | 🥉 Fast | Tasks simples, baixo custo, alto throughput | 200K tokens |

### OpenAI — GPT-5.x / Codex

| Modelo | Tier | Foco Principal | Janela de Contexto |
|--------|------|---------------|-------------------|
| **GPT-5.5** | 🥇 Premium | Agentic, raciocínio avançado, multi-step | 128K tokens |
| **GPT-5 / o3** | 🥈 Balanced | Raciocínio científico, matemática, código | 128K tokens |
| **Codex 5.3** | ⚡ Code-Spec | Código puro, terminal, autocomplete, n8n | 64K tokens |

### Google — Gemini 3.x

| Modelo | Tier | Foco Principal | Janela de Contexto |
|--------|------|---------------|-------------------|
| **Gemini 3.1 Ultra** | 🥇 Premium | Multimodal, ciência, raciocínio | 1M tokens |
| **Gemini 3.1 Pro** | 🥈 Balanced | Custo-benefício, OCR, visão, código | 1M tokens |
| **Gemini 3.1 Flash** | ⚡ Fast | Alta velocidade, baixo custo, produção | 1M tokens |

### Bônus — DeepSeek R2

| Modelo | Tier | Foco Principal | Janela de Contexto |
|--------|------|---------------|-------------------|
| **DeepSeek R2** | 🥇 Open-Source Premium | Código, matemática, raciocínio, custo zero (self-host) | 128K tokens |

> **Por que DeepSeek R2?** Alcança performance comparável ao GPT-5 e Claude Opus em benchmarks de código e raciocínio, com custo de API drasticamente menor e opção de self-host. Excelente para times que precisam de alto volume a custo controlado.

---

## 📊 Tabela de Benchmarks Gerais

> Scores baseados em benchmarks públicos consolidados (HumanEval, MMLU, SWE-bench, GPQA, LiveCodeBench, Agentic Task Success Rate — 2025/2026).

| Benchmark | Claude Opus 4.7 | Claude Sonnet 4.6 | GPT-5.5 | Codex 5.3 | Gemini 3.1 Pro | DeepSeek R2 |
|-----------|:-:|:-:|:-:|:-:|:-:|:-:|
| **HumanEval** (Código Geral) | 91.2% | 87.4% | 90.8% | **95.1%** | 88.6% | 92.3% |
| **SWE-bench** (Bugs Reais) | 72.1% | 65.3% | 70.4% | **76.8%** | 63.2% | 70.9% |
| **MMLU** (Conhecimento Geral) | **89.4%** | 84.1% | 88.7% | 81.2% | 87.9% | 86.5% |
| **GPQA** (Raciocínio Expert) | **78.3%** | 68.9% | 76.1% | 64.5% | 74.8% | 72.6% |
| **Agentic Task Success** | 74.2% | 65.1% | **81.5%** | 69.3% | 70.2% | 73.8% |
| **LiveCodeBench** (Código Atual) | 78.4% | 74.2% | 79.1% | **88.3%** | 73.5% | 81.2% |
| **MATH** (Matemática Avançada) | **84.7%** | 76.3% | 83.9% | 74.1% | 82.4% | 83.1% |
| **Multilingual** | **86.2%** | 82.4% | 83.7% | 77.3% | 85.1% | 79.8% |
| **Instruction Following** | **91.8%** | 88.3% | 89.4% | 82.7% | 87.6% | 85.3% |
| **JSON / Schema Output** | **93.1%** | 89.7% | 91.2% | 85.4% | 88.3% | 87.9% |

> 🏆 **Legenda:** Negrito = melhor do grupo naquele benchmark | Scores são médias aproximadas consolidadas de múltiplas fontes públicas.

---

## 🔍 Comparativo por Caso de Uso

---

### 1. JavaScript — Node Code n8n

> Contexto: escrever, debugar e refatorar código JavaScript nos nodes `Code` do n8n, manipulação de `$input`, `$json`, chamadas a APIs externas, transformação de arrays/objetos.

| Critério | Codex 5.3 | Claude Sonnet 4.6 | GPT-5.5 | Claude Opus 4.7 | Gemini 3.1 Pro | DeepSeek R2 |
|---------|:---------:|:-----------------:|:-------:|:---------------:|:--------------:|:-----------:|
| **Velocidade de resposta** | ⚡⚡⚡⚡⚡ | ⚡⚡⚡⚡ | ⚡⚡⚡ | ⚡⚡ | ⚡⚡⚡⚡ | ⚡⚡⚡ |
| **Qualidade do código JS** | 🥇 95.1% | 🥉 87.4% | 🥈 90.8% | 87.2% | 88.6% | 92.3% |
| **Entende contexto n8n** | ✅ Excelente | ✅ Muito bom | ✅ Bom | ✅ Muito bom | ⚠️ Moderado | ⚠️ Moderado |
| **Manipulação `$json`/`$input`** | ✅✅ | ✅✅ | ✅ | ✅✅ | ✅ | ✅ |
| **Async/await complexo** | ✅✅ | ✅✅ | ✅✅ | ✅✅✅ | ✅ | ✅✅ |
| **Tratamento de erros** | ✅✅ | ✅✅ | ✅✅ | ✅✅✅ | ✅✅ | ✅✅ |
| **Loops e transformações** | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ | ✅✅ | ✅✅ |
| **Custo por tarefa** | 💰💰 | 💰💰 | 💰💰💰 | 💰💰💰💰 | 💰 | 💰 |

**🏆 Recomendação para JavaScript n8n:**

```
Uso diário / iteração rápida   → Codex 5.3
Refatoração e arquitetura      → Claude Opus 4.7
Balanceado velocidade/custo    → Claude Sonnet 4.6
Alto volume / self-host        → DeepSeek R2
```

**Exemplo de prompt ideal para Codex 5.3 no node Code n8n:**
```
Crie um node Code n8n em JavaScript que:
1. Recebe $input.all() com array de pedidos
2. Agrupa por customer_id
3. Soma o total de cada cliente
4. Retorna array no formato [{customer_id, total, count}]
Inclua tratamento de erro e valide se o campo existe antes de acessar.
```

---

### 2. Python — Scripts e Automações

> Contexto: scripts Python para automações, integração com APIs, processamento de dados, uso em LangChain/LangGraph, nodes Python do n8n.

| Critério | Codex 5.3 | Claude Opus 4.7 | GPT-5.5 | Claude Sonnet 4.6 | Gemini 3.1 Pro | DeepSeek R2 |
|---------|:---------:|:---------------:|:-------:|:-----------------:|:--------------:|:-----------:|
| **Qualidade geral Python** | 🥇 | 🥉 | 🥈 | ✅ | ✅ | ✅✅ |
| **Data Science / Pandas** | ✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅✅ |
| **FastAPI / async** | ✅✅✅ | ✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅✅ |
| **Integração com APIs** | ✅✅✅ | ✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ |
| **LangChain / LangGraph** | ✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ |
| **Pydantic / Typing** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅✅ |
| **Geração de testes (pytest)** | ✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ | ✅✅ |
| **Custo** | 💰💰 | 💰💰💰💰 | 💰💰💰 | 💰💰 | 💰 | 💰 |

**🏆 Recomendação para Python:**

```
Scripts rápidos e integração   → Codex 5.3
LangChain / Agentes complexos  → GPT-5.5 ou Claude Opus 4.7
Data processing / Pandas       → Gemini 3.1 Pro ou DeepSeek R2
Custo baixo / alto volume      → DeepSeek R2
```

---

### 3. Fluxos n8n — JSON, Nodes e Workflows

> Contexto: gerar JSON completo de workflows n8n, criar sequências de nodes, configurar credenciais, entender a estrutura interna de fluxos exportados.

| Critério | Claude Opus 4.7 | GPT-5.5 | Claude Sonnet 4.6 | Codex 5.3 | Gemini 3.1 Pro | DeepSeek R2 |
|---------|:---------------:|:-------:|:-----------------:|:---------:|:--------------:|:-----------:|
| **Geração de JSON válido n8n** | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅✅ | ✅✅ | ✅✅ |
| **Estrutura de conexões** | ✅✅✅ | ✅✅ | ✅✅ | ✅✅✅ | ✅ | ✅✅ |
| **Nodes especializados** (HTTP, Webhook, IF) | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅✅ | ✅✅ | ✅✅ |
| **Raciocínio sobre fluxo lógico** | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ | ✅✅ |
| **Integração com APIs externas** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ |
| **Sub-workflows e chamadas internas** | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ | ✅ | ✅✅ |
| **Debugging de fluxos quebrados** | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ | ✅✅ |

**🏆 Recomendação para n8n Workflows:**

```
Criar fluxos complexos do zero  → Claude Opus 4.7
Geração rápida de JSON          → Codex 5.3
Debugging e ajuste de fluxo     → GPT-5.5
Uso balanceado no dia a dia     → Claude Sonnet 4.6
```

**Exemplo de prompt para geração de workflow n8n:**
```
Gere o JSON completo de um workflow n8n que:
- Webhook POST recebe dados de pedido
- Valida campos obrigatórios com node IF
- Consulta Supabase para verificar cliente
- Envia notificação Slack se novo cliente
- Salva no Postgres com upsert
- Retorna resposta JSON com status

Formate como JSON exportável, válido para importar no n8n 1.x
```

---

### 4. LangChain, LangGraph e LangSmith

> Contexto: implementar chains, grafos de agentes, rastreamento de execução, tools/functions, memory management, retrieval augmented generation (RAG).

| Critério | Claude Opus 4.7 | GPT-5.5 | Gemini 3.1 Pro | Claude Sonnet 4.6 | Codex 5.3 | DeepSeek R2 |
|---------|:---------------:|:-------:|:--------------:|:-----------------:|:---------:|:-----------:|
| **LangChain Chains complexas** | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ | ✅✅ |
| **LangGraph — grafos de estado** | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ | ✅✅ |
| **LangSmith — debugging traces** | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ | ✅ | ✅ |
| **Tool calling / Function calling** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ |
| **RAG — Retrieval pipelines** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ |
| **Memory de curto/longo prazo** | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅ | ✅✅ |
| **LCEL (LangChain Expression Language)** | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ | ✅✅ |
| **Multi-agent orchestration** | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅ | ✅✅ |

**🏆 Recomendação para LangChain/LangGraph:**

```
Arquitetura de grafos complexos    → Claude Opus 4.7 ou GPT-5.5
RAG pipeline e retrieval           → GPT-5.5 ou Gemini 3.1 Pro
Tool calling e function design     → GPT-5.5
Custo reduzido em produção         → DeepSeek R2 (self-host)
```

**Exemplo de estrutura LangGraph recomendada:**
```python
# Use Claude Opus 4.7 para criar a arquitetura inicial
# Use GPT-5.5 para implementar os tool calls
# Use Gemini 3.1 Flash para o nó de retrieval (custo)
# Use LangSmith com Opus para debug dos traces
```

---

### 5. Agentes IA — Conversacionais

> Contexto: agentes que conversam com usuários finais, mantêm contexto, respondem perguntas, encaminham para ferramentas, suporte ao cliente, assistentes internos.

| Critério | GPT-5.5 | Claude Opus 4.7 | Claude Sonnet 4.6 | Gemini 3.1 Pro | DeepSeek R2 | Codex 5.3 |
|---------|:-------:|:---------------:|:-----------------:|:--------------:|:-----------:|:---------:|
| **Naturalidade conversacional** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ❌ |
| **Manutenção de contexto longo** | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅✅ | ✅✅ | ✅ |
| **Personalidade e tom consistente** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ❌ |
| **Seguir system prompt rigoroso** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅ |
| **Resistência a jailbreak** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ |
| **Multilingual (PT-BR)** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ |
| **Agentic task success** | 🥇 81.5% | 🥉 74.2% | 65.1% | 🥈 70.2% | 73.8% | 69.3% |
| **Custo por 1M tokens** | 💰💰💰 | 💰💰💰💰 | 💰💰 | 💰 | 💰 | 💰💰 |

**🏆 Recomendação para Agentes Conversacionais:**

```
Agente premium / missão crítica    → GPT-5.5 ou Claude Opus 4.7
Produção com volume médio          → Claude Sonnet 4.6
Alto volume / custo controlado     → Gemini 3.1 Flash ou DeepSeek R2
Multilingual PT-BR robusto         → Claude Sonnet 4.6 ou GPT-5.5
```

---

### 6. Engenharia de Prompts para Agentes

> Contexto: criar, refinar e iterar prompts complexos para agentes — system prompts com regras, schemas JSON, few-shot examples, políticas anti-alucinação, XML estruturado para ferramentas.

| Critério | Claude Opus 4.7 | GPT-5.5 | Claude Sonnet 4.6 | Gemini 3.1 Pro | Codex 5.3 | DeepSeek R2 |
|---------|:---------------:|:-------:|:-----------------:|:--------------:|:---------:|:-----------:|
| **Estruturação de system prompts** | 🥇 ✅✅✅ | 🥈 ✅✅✅ | ✅✅ | ✅✅ | ✅ | ✅✅ |
| **Regras anti-alucinação** | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅ | ✅✅ |
| **Few-shot examples eficientes** | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅ | ✅✅ |
| **Schemas JSON em prompt** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅✅ | ✅✅ |
| **XML estruturado (Claude-style)** | ✅✅✅ | ✅✅ | ✅✅✅ | ✅ | ✅ | ✅ |
| **Refinamento iterativo** | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅ | ✅✅ |
| **Prompt para tool use** | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ | ✅✅ |
| **Meta-prompting** | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅ | ✅✅ |

**🏆 Recomendação para Prompt Engineering:**

```
Criar prompt complexo do zero       → Claude Opus 4.7 (melhor)
Testar comportamento agentic        → GPT-5.5
Iterar rapidamente                  → Claude Sonnet 4.6
Estruturar schemas JSON no prompt   → Claude Opus 4.7 ou GPT-5.5
```

**Por que Opus 4.7 é o melhor para prompt engineering?**

> O Claude Opus 4.7 chega a soluções ótimas em menos iterações quando o objetivo é refinar estruturas complexas (XML com políticas, schemas de validação, handling de erros), com `Instruction Following Score: 91.8%` — o mais alto entre os modelos testados. Isso significa que o prompt que você cria **com** o Opus tende a funcionar melhor **em** qualquer outro modelo.

---

### 7. OCR e Extração Estruturada de Dados

> Contexto: prompts para ferramentas de OCR, extração de dados de imagens/documentos, parsing de menus, notas fiscais, contratos, formulários.

| Critério | Gemini 3.1 Pro | Claude Opus 4.7 | GPT-5.5 | Claude Sonnet 4.6 | Gemini 3.1 Flash | DeepSeek R2 |
|---------|:--------------:|:---------------:|:-------:|:-----------------:|:----------------:|:-----------:|
| **Compreensão de imagem** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅✅ | ✅ |
| **Extração de tabelas** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅✅ | ✅ |
| **Parsing de texto não estruturado** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ |
| **Output JSON estruturado** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ |
| **Resistência a alucinação** | ✅✅ | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ |
| **Custo para alto volume** | 💰💰 | 💰💰💰💰 | 💰💰💰 | 💰💰 | 💰 | 💰 |
| **Velocidade** | ⚡⚡⚡⚡ | ⚡⚡ | ⚡⚡⚡ | ⚡⚡⚡⚡ | ⚡⚡⚡⚡⚡ | ⚡⚡⚡ |

**🏆 Recomendação para OCR:**

```
Criar o prompt OCR (design)         → Claude Opus 4.7
Rodar em produção (custo/volume)    → Gemini 3.1 Flash
Qualidade máxima de extração        → Gemini 3.1 Pro ou GPT-5.5
Anti-alucinação crítica             → Claude Opus 4.7 ou GPT-5.5
```

**Estrutura recomendada de prompt OCR (criado com Opus 4.7):**
```xml
<system>
  Você é um extrator especializado. Extraia apenas o que está visível.
  NUNCA invente informações. Se incerto, use null.
  
  <output_schema>
  {
    "items": [{"name": string, "price": number, "category": string}],
    "total": number | null,
    "confidence": "high" | "medium" | "low"
  }
  </output_schema>
  
  <rules>
  - Se preço ilegível → null (não adivinhe)
  - Se nome ambíguo → transcreva exatamente
  - Retorne APENAS JSON válido
  </rules>
</system>
```

---

### 8. Estrutura de Dados — Redis, Postgres, Supabase

> Contexto: queries SQL, modelagem de schema, operações Redis, integração via Supabase JS/Python SDK, design de índices, funções de banco.

| Critério | Claude Opus 4.7 | GPT-5.5 | DeepSeek R2 | Claude Sonnet 4.6 | Gemini 3.1 Pro | Codex 5.3 |
|---------|:---------------:|:-------:|:-----------:|:-----------------:|:--------------:|:---------:|
| **SQL avançado (CTEs, Windows)** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅✅ | ✅✅ |
| **Modelagem de schema** | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ | ✅ |
| **Supabase RLS (Row Level Security)** | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ | ✅ |
| **Redis patterns (pub/sub, streams)** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ |
| **Supabase Edge Functions** | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅✅ | ✅✅ | ✅✅ |
| **Indexes e performance** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅✅ | ✅✅ |
| **Postgres Functions / Triggers** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ |
| **Migrations e versionamento** | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ | ✅ |

**🏆 Recomendação para dados:**

```
Design de schema e arquitetura      → Claude Opus 4.7 ou GPT-5.5
Queries SQL complexas               → GPT-5.5 ou DeepSeek R2
Supabase RLS e policies             → Claude Opus 4.7
Redis patterns para cache           → GPT-5.5 ou DeepSeek R2
Volume alto / custo baixo           → DeepSeek R2
```

**Exemplo de prompt para Supabase com n8n:**
```
Contexto: workflow n8n que precisa:
1. Upsert de usuário no Postgres (Supabase)
2. Cache de sessão no Redis (TTL 1h)
3. RLS para que usuário só veja seus próprios dados

Gere:
- Schema SQL com índices necessários
- RLS policy correta
- Código JS para o node Code n8n que faz o upsert via Supabase SDK
- Código JS para set/get no Redis com ioredis
```

---

### 9. Arquitetura e Refatoração de Código

> Contexto: revisar código existente, propor refatoração, identificar code smells, sugerir padrões (SOLID, DDD, Clean Code), documentar decisões técnicas.

| Critério | Claude Opus 4.7 | GPT-5.5 | DeepSeek R2 | Claude Sonnet 4.6 | Gemini 3.1 Pro | Codex 5.3 |
|---------|:---------------:|:-------:|:-----------:|:-----------------:|:--------------:|:---------:|
| **Identificação de code smells** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ |
| **Proposta de arquitetura** | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ | ✅ |
| **SOLID / Design Patterns** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ |
| **Documentação técnica (ADR)** | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅✅ | ✅✅ | ✅ |
| **Refatoração incremental** | ✅✅✅ | ✅✅ | ✅✅✅ | ✅✅ | ✅✅ | ✅✅✅ |
| **Contexto de janela longa** | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ | ✅✅✅ | ✅ |

**🏆 Recomendação:** `Claude Opus 4.7` é líder aqui pela janela de contexto longa (200K), raciocínio profundo e `Instruction Following 91.8%` — perfeito para revisar arquivos grandes e produzir ADRs.

---

### 10. Geração de JSON e Schemas

> Contexto: gerar JSON de configuração, schemas Zod/Pydantic/JSON Schema, outputs estruturados de LLMs, parsing de respostas.

| Critério | Claude Opus 4.7 | Claude Sonnet 4.6 | Codex 5.3 | GPT-5.5 | Gemini 3.1 Pro | DeepSeek R2 |
|---------|:---------------:|:-----------------:|:---------:|:-------:|:--------------:|:-----------:|
| **JSON válido sempre** | 🥇 93.1% | 89.7% | 85.4% | 91.2% | 88.3% | 87.9% |
| **Schemas Zod complexos** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ |
| **Pydantic v2** | ✅✅✅ | ✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅✅ |
| **OpenAPI spec** | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅✅ | ✅✅ | ✅✅ |
| **n8n workflow JSON** | ✅✅✅ | ✅✅ | ✅✅✅ | ✅✅✅ | ✅ | ✅✅ |

**🏆 Recomendação:** Para saídas JSON críticas (schemas, configs, workflows), use `Claude Opus 4.7` — maior score de JSON válido (93.1%) e melhor `Instruction Following`.

---

### 11. Debug e Resolução de Erros

> Contexto: colar stack trace, erro de runtime, comportamento inesperado de fluxo n8n, erro de integração API.

| Critério | GPT-5.5 | Claude Opus 4.7 | Codex 5.3 | Claude Sonnet 4.6 | DeepSeek R2 | Gemini 3.1 Pro |
|---------|:-------:|:---------------:|:---------:|:-----------------:|:-----------:|:--------------:|
| **Identificação rápida do erro** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ |
| **Explicação clara da causa** | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅✅ | ✅✅ | ✅✅ |
| **Solução funcional gerada** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅✅ | ✅✅ |
| **Contexto n8n específico** | ✅✅ | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅ |
| **Velocidade de resposta** | ⚡⚡⚡ | ⚡⚡ | ⚡⚡⚡⚡⚡ | ⚡⚡⚡⚡ | ⚡⚡⚡ | ⚡⚡⚡⚡ |

**🏆 Recomendação para Debug:**

```
Debug rápido de JavaScript/n8n  → Codex 5.3 (velocidade máxima)
Debug de lógica complexa        → Claude Opus 4.7 ou GPT-5.5
Debug iterativo no dia a dia    → Claude Sonnet 4.6
```

---

### 12. Multimodal — Imagem, Vídeo e Documentos

> Contexto: análise de imagens, screenshots de interfaces, PDFs, dados de vídeo, geração de descrições estruturadas.

| Critério | Gemini 3.1 Ultra | GPT-5.5 | Claude Opus 4.7 | Gemini 3.1 Pro | Claude Sonnet 4.6 | Gemini 3.1 Flash |
|---------|:----------------:|:-------:|:---------------:|:--------------:|:-----------------:|:----------------:|
| **Análise de imagem** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅✅ |
| **PDF / documentos** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ |
| **Vídeo** | ✅✅✅ | ❌ | ❌ | ✅✅✅ | ❌ | ✅✅✅ |
| **Janela de contexto para docs** | 🥇 1M | 128K | 200K | 🥇 1M | 200K | 🥇 1M |
| **Output estruturado de imagem** | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ |
| **Custo** | 💰💰💰💰 | 💰💰💰 | 💰💰💰💰 | 💰💰 | 💰💰 | 💰 |

**🏆 Recomendação para Multimodal:**

```
Análise de vídeo                  → Gemini 3.1 Pro/Flash (exclusivo)
OCR em alto volume                → Gemini 3.1 Flash
Extração crítica de documentos    → GPT-5.5 ou Claude Opus 4.7
PDFs com contexto enorme          → Gemini 3.1 Pro (1M tokens)
```

---

## 🗺️ Matriz de Decisão Rápida

> Use esta tabela para uma decisão em 30 segundos.

| Tarefa | 🥇 Melhor | 🥈 Alternativa | 🥉 Custo Baixo |
|--------|-----------|---------------|----------------|
| JS Node Code n8n (diário) | **Codex 5.3** | Claude Sonnet 4.6 | DeepSeek R2 |
| JS Node Code n8n (complexo) | **Claude Opus 4.7** | GPT-5.5 | DeepSeek R2 |
| Python scripts gerais | **Codex 5.3** | GPT-5.5 | DeepSeek R2 |
| Python LangChain/LangGraph | **GPT-5.5** | Claude Opus 4.7 | DeepSeek R2 |
| Criar workflow n8n JSON | **Claude Opus 4.7** | Codex 5.3 | Claude Sonnet 4.6 |
| Debug de fluxo n8n | **Codex 5.3** | Claude Sonnet 4.6 | DeepSeek R2 |
| Prompt engineering agentes | **Claude Opus 4.7** | GPT-5.5 | Claude Sonnet 4.6 |
| Agente conversacional produção | **GPT-5.5** | Claude Sonnet 4.6 | Gemini 3.1 Flash |
| OCR / extração de imagens | **Gemini 3.1 Pro** | GPT-5.5 | Gemini 3.1 Flash |
| SQL avançado / Supabase | **GPT-5.5** | Claude Opus 4.7 | DeepSeek R2 |
| Redis / cache design | **GPT-5.5** | DeepSeek R2 | Claude Sonnet 4.6 |
| LangGraph grafos de estado | **Claude Opus 4.7** | GPT-5.5 | DeepSeek R2 |
| Refatoração de arquitetura | **Claude Opus 4.7** | GPT-5.5 | DeepSeek R2 |
| Schema JSON / Zod / Pydantic | **Claude Opus 4.7** | GPT-5.5 | Codex 5.3 |
| Geração de documentação | **Claude Opus 4.7** | Claude Sonnet 4.6 | Gemini 3.1 Pro |
| Análise de vídeo | **Gemini 3.1 Pro** | Gemini 3.1 Flash | ❌ |
| Alto volume / custo crítico | **DeepSeek R2** | Gemini 3.1 Flash | Gemini 3.1 Flash |

---

## 👤 Guia de Escolha por Perfil

### 🧑‍💻 Desenvolvedor Solo — n8n + JavaScript

```
Stack diária:
├── Codex 5.3        → Escrever código JS nos nodes
├── Claude Sonnet 4.6 → Debug rápido e ajustes
└── Claude Opus 4.7  → Quando travar em algo complexo

Regra: Começa com Codex, escala para Opus quando necessário.
```

### 🤖 Engenheiro de Agentes IA

```
Stack recomendada:
├── Claude Opus 4.7  → Criar e refinar system prompts
├── GPT-5.5          → Testar comportamento agentic
├── Claude Sonnet 4.6 → Iterações rápidas de prompt
└── Gemini 3.1 Flash → Rodar em produção (custo)

Regra: Design com Opus, produção com Flash/Sonnet.
```

### 🏗️ Arquiteto de Automações (LangChain + n8n)

```
Stack recomendada:
├── Claude Opus 4.7  → Arquitetura, LangGraph, schemas
├── GPT-5.5          → Tool calling, function design
├── Codex 5.3        → Código de integração rápido
└── DeepSeek R2      → Workloads de alto volume

Regra: Arquitetura com Opus, execução com Codex/DeepSeek.
```

### 🗃️ Engenheiro de Dados (Supabase + Redis + Postgres)

```
Stack recomendada:
├── GPT-5.5          → SQL avançado e RLS policies
├── DeepSeek R2      → Queries de análise, custo baixo
├── Claude Opus 4.7  → Modelagem e documentação de schema
└── Claude Sonnet 4.6 → Consultas do dia a dia

Regra: GPT-5.5 para complexidade, DeepSeek para volume.
```

---

## 💰 Custo-Benefício e Velocidade

> Referência relativa de custo (💰 = mais barato → 💰💰💰💰 = mais caro) e velocidade.

| Modelo | Custo Relativo | Tokens/seg (aprox.) | Ideal para |
|--------|:--------------:|:-------------------:|-----------|
| **Gemini 3.1 Flash** | 💰 | ~200 t/s | Produção alto volume, OCR |
| **DeepSeek R2** | 💰 | ~120 t/s | Código, dados, self-host |
| **Claude Haiku 4.5** | 💰💰 | ~180 t/s | Tasks simples, triagem |
| **Gemini 3.1 Pro** | 💰💰 | ~100 t/s | Multimodal, visão, RAG |
| **Claude Sonnet 4.6** | 💰💰 | ~110 t/s | Balanceado, dia a dia |
| **Codex 5.3** | 💰💰 | ~150 t/s | Código puro, terminal |
| **GPT-5.5** | 💰💰💰 | ~80 t/s | Agentic, multi-step |
| **Claude Opus 4.7** | 💰💰💰💰 | ~40 t/s | Tarefas críticas e complexas |
| **Gemini 3.1 Ultra** | 💰💰💰💰 | ~50 t/s | Multimodal premium |

---

## 🏗️ Stack Recomendada por Projeto

### Projeto: Automação n8n Completa (pequeno time)

```yaml
Criação de fluxos e lógica:    Claude Opus 4.7
Código JS nos nodes:           Codex 5.3
Debug e ajustes rápidos:       Claude Sonnet 4.6
Integração banco de dados:     GPT-5.5
Em produção (agente):          Claude Sonnet 4.6
```

### Projeto: Plataforma de Agentes IA Conversacionais

```yaml
Engenharia de prompts:         Claude Opus 4.7
Testes de comportamento:       GPT-5.5
Agente em produção:            Claude Sonnet 4.6 ou Gemini 3.1 Flash
Memória e RAG:                 GPT-5.5
Monitoring (LangSmith):        Claude Opus 4.7 para análise
```

### Projeto: Pipeline OCR + Extração de Dados

```yaml
Design do prompt OCR:          Claude Opus 4.7
Modelo de produção:            Gemini 3.1 Flash (velocidade/custo)
Fallback de qualidade:         Gemini 3.1 Pro
Estrutura de dados:            Supabase + Claude Sonnet 4.6 para queries
```

### Projeto: Sistema RAG com LangChain + Supabase

```yaml
Arquitetura do grafo:          Claude Opus 4.7
Tool calling e funções:        GPT-5.5
Retrieval e embeddings:        Gemini 3.1 Pro
SQL e Supabase RLS:            GPT-5.5
Código de integração:          Codex 5.3
Produção (inference):          DeepSeek R2 (custo) ou Sonnet 4.6
```

---

## 🔄 Fluxograma de Decisão

```
Qual é a tarefa?
│
├── CÓDIGO PURO (JS/Python)
│   ├── Rápido / iterativo?         → Codex 5.3
│   ├── Arquitetura / complexo?     → Claude Opus 4.7
│   └── Custo baixo / volume?       → DeepSeek R2
│
├── AUTOMAÇÃO n8n
│   ├── Escrever node Code?         → Codex 5.3
│   ├── Criar workflow JSON?        → Claude Opus 4.7
│   └── Debug de fluxo?             → Claude Sonnet 4.6
│
├── AGENTES IA
│   ├── Criar prompt do agente?     → Claude Opus 4.7
│   ├── Testar comportamento?       → GPT-5.5
│   └── Produção conversacional?    → Claude Sonnet 4.6
│
├── OCR / VISÃO
│   ├── Design do prompt?           → Claude Opus 4.7
│   ├── Produção alto volume?       → Gemini 3.1 Flash
│   └── Máxima qualidade?           → Gemini 3.1 Pro
│
├── DADOS (SQL/Redis/Supabase)
│   ├── SQL complexo / RLS?         → GPT-5.5
│   ├── Redis patterns?             → GPT-5.5 ou DeepSeek R2
│   └── Modelagem de schema?        → Claude Opus 4.7
│
└── LANGCHAIN / LANGGRAPH
    ├── Arquitetura do grafo?       → Claude Opus 4.7
    ├── Tool calling?               → GPT-5.5
    └── RAG / retrieval?            → GPT-5.5 ou Gemini 3.1 Pro
```

---

## 📝 Resumo Final — TL;DR

| Se você precisa de... | Use |
|----------------------|-----|
| **Melhor modelo geral para código JS n8n** | 🥇 Codex 5.3 |
| **Melhor para tarefas difíceis e ambíguas** | 🥇 Claude Opus 4.7 |
| **Melhor para agentes agentic** | 🥇 GPT-5.5 |
| **Melhor custo-benefício no dia a dia** | 🥇 Claude Sonnet 4.6 |
| **Melhor para OCR e visão** | 🥇 Gemini 3.1 Pro |
| **Melhor para alto volume barato** | 🥇 Gemini 3.1 Flash |
| **Melhor open-source / self-host** | 🥇 DeepSeek R2 |
| **Melhor para prompt engineering** | 🥇 Claude Opus 4.7 |

---

## 📚 Fontes e Referências

| Fonte | Tema |
|-------|------|
| [DataCamp — GPT-5.5 vs Claude Opus 4.7](https://www.datacamp.com/pt/blog/gpt-5-5-vs-claude-opus-4-7) | Comparativo geral |
| [NXCode — Codex 5.3 vs Opus 4.6](https://www.nxcode.io/pt/resources/news/gpt-5-3-codex-vs-claude-opus-4-6-ai-coding-comparison-2026) | Coding benchmark 2026 |
| [NXCode — Sonnet 4.6 vs GPT-5.5](https://www.nxcode.io/pt/resources/news/claude-sonnet-4-6-vs-gpt-5-4-coding-comparison-2026) | Coding comparison 2026 |
| [Adaline.ai — Top Agentic LLMs 2026](https://www.adaline.ai/blog/top-agentic-llm-models-frameworks-for-2026) | Agentic benchmarks |
| [BenchLM — Sonnet 4.6 vs GPT-5.5](https://benchlm.ai/compare/claude-sonnet-4-6-vs-gpt-5-5) | Benchmark detalhado |
| [Iternal.ai — LLM Selection Guide](https://iternal.ai/llm-selection-guide) | Guia de seleção |
| [NXCode — Gemini 3.1 vs Claude vs GPT](https://www.nxcode.io/resources/news/gemini-3-1-pro-vs-claude-opus-4-6-vs-gpt-5-comparison-2026) | Comparativo 3 famílias |
| [AkitaOnRails — Benchmarks Parte 3](https://www.akitaonrails.com/2026/04/24/llm-benchmarks-parte-3-deepseek-kimi-mimo/) | DeepSeek e outros |
| [DocsBot — GPT-5.5 vs Sonnet 4.6](https://docsbot.ai/models/compare/gpt-5-5/claude-sonnet-4-6) | Comparativo detalhado |

---

## 🤝 Contribuindo

Encontrou um benchmark mais atualizado? Tem experiência prática diferente do que está aqui?

1. Abra uma **Issue** descrevendo a discrepância
2. Faça um **PR** com a atualização e a fonte
3. Inclua data e link do benchmark utilizado

---

## ⚠️ Disclaimer

> Os scores de benchmark neste documento são aproximações consolidadas de múltiplas fontes públicas (maio 2026). Modelos são atualizados frequentemente — performance pode variar com versões de API, temperatura, sistema de prompts e contexto de uso. Sempre faça seus próprios testes para casos de uso críticos em produção.

---

<div align="center">

**Feito para a comunidade de desenvolvedores, automadores e engenheiros de IA** 🚀

*Se este guia foi útil, deixa uma ⭐ no repositório!*

</div>
