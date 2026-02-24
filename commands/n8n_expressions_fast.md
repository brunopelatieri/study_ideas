# 🏢 n8n JavaScript Expressions – Enterprise Guide
_Advanced Enterprise Reference & Comparative Expression Table_

---

## 🇧🇷 Visão Geral

Este documento é a versão **Enterprise** do guia de expressões JavaScript para n8n.
Ele foi estruturado para:

- Padronização interna de times
- Documentação corporativa
- Onboarding técnico
- Consulta rápida (cheatsheet avançado)
- Governança de automações

---

## 🇺🇸 Overview

This is the **Enterprise Edition** of the JavaScript expressions guide for n8n.
Designed for:

- Internal standardization
- Corporate documentation
- Technical onboarding
- Quick reference
- Automation governance

---

# 🔎 Fundamentos das Expressões no n8n

No n8n, expressões são escritas dentro de:

```handlebars
{{ ... }}
```

Permitem:

- Acessar dados do node atual (`$json`)
- Acessar dados de outros nodes (`$('NodeName').item.json`)
- Manipular strings e arrays
- Executar lógica JavaScript inline

---

# 📊 Tabela Comparativa – Expressões Comuns

| Objetivo | Expressão | Quando Usar | Alternativa Antiga | Recomendação Enterprise |
|-----------|------------|-------------|---------------------|--------------------------|
| Fallback de valor | `a || b || "default"` | Garantir valor mínimo | if ternário longo | ✔ Usar operador OR |
| Concatenar valores | `a + "." + b` | Montar IDs ou nomes | Template string manual | ✔ OK para simples |
| Último item array | `array.at(-1)` | Pegar último elemento | `array[array.length - 1]` | ✔ Preferir `.at()` |
| Quebrar string | `split('.')` | Separar extensão | regex complexa | ✔ Usar split simples |
| Remover não dígitos | `replace(/\D/g, '')` | Normalizar telefone | Loop manual | ✔ Regex eficiente |
| Executar lógica complexa | IIFE `(() => {...})()` | Condições múltiplas | Code Node | ⚠ Use com moderação |
| Acessar outro node | `$('Node').item.json.field` | Dependência entre nodes | Copiar manual | ✔ Padrão oficial |
| Validar existência | `a ? a : b` | Controle explícito | OR simples | ✔ Preferir OR quando possível |

---

# 🧠 Snippets Enterprise Padronizados

## 1️⃣ Fallback Seguro

```handlebars
{{ $json.primary || $json.backup || "N/A" }}
```

---

## 2️⃣ Extração de Extensão de Arquivo

```handlebars
{{ $json.filename.split('.').at(-1) }}
```

---

## 3️⃣ Normalização de Telefone

```handlebars
{{ $json.phone?.replace(/\D/g, '') || "" }}
```

✔ Uso de optional chaining  
✔ Seguro contra null/undefined

---

## 4️⃣ Extração Segura de Node Externo

```handlebars
{{ $('Webhook1').item.json?.body?.data?.id || "ID_INEXISTENTE" }}
```

✔ Evita erro se campo não existir

---

# 🛡️ Padrões de Governança Enterprise

### ✔ Sempre usar fallback
Evita quebra de workflow.

### ✔ Preferir `.at(-1)`
Mais legível e moderno.

### ✔ Usar optional chaining `?.`
Previne erro em JSON inconsistente.

### ✔ Documentar expressões críticas
Colocar comentário no node.

### ✔ Evitar lógica complexa inline
Se passar de 3 operações → use Code Node.

---

# 🚨 Anti‑Patterns

❌ Expressões com múltiplos ternários aninhados  
❌ Regex excessivamente complexa inline  
❌ Dependência circular entre nodes  
❌ Falta de fallback em variáveis críticas  

---

# 📈 Boas Práticas para Times

- Criar biblioteca interna de snippets
- Padronizar nomenclatura de nodes
- Versionar workflows críticos
- Testar com dados reais antes de produção
- Documentar fluxos com Mermaid no README

---

# 📚 Referência Oficial

- https://docs.n8n.io/code/expressions/

---

# 👤 Autor

Bruno Pelatieri Goulart  
Enterprise Automation Architect • AI • DevOps • n8n Specialist
