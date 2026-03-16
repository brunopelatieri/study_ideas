# 🏢 n8n JavaScript Expressions – Fast + Enterprise Unified Guide
_Advanced Quick Reference + Enterprise Governance Pattern_

---

## 📌 Documento Consolidado

Este guia consolida:

- Guia Enterprise de Expressões
- Guia Técnico de Datas e Timestamps
- Concatenação e manipulação avançada

Agora estruturado no padrão oficial de documentação técnica.

---

# 🇧🇷 Visão Geral

Este documento reúne:

- Expressões rápidas para uso diário
- Concatenação avançada entre nodes
- Manipulação segura de strings e arrays
- Datas e timestamps (modelo Pareto 80/20)
- Padrões Enterprise de governança
- Anti‑patterns críticos
- Cheatsheet de produção

---

# 🔎 Fundamentos das Expressões no n8n

Expressões são escritas dentro de:

```handlebars
{{ ... }}
```

Permitem:

- Acessar `$json`
- Acessar outros nodes `$('Node').item.json`
- Executar JavaScript inline
- Manipular datas via `$now`
- Usar Luxon internamente (DateTime)

### Ex.: No Code node do n8n, a sintaxe correta é:

```javascript
const userId = $('VarBD').first().json.app_user_id;
```

Essa sintaxe **já está correta**. Mas para funcionar, o nó `VarBD` precisa estar **conectado ao fluxo** que chega até o Code node, mesmo que não seja o nó imediatamente anterior.

---

Se o nó estiver distante no fluxo e der erro, use:

```javascript
// Opção 1 — nó pelo nome exato (mais comum)
const userId = $('VarBD').first().json.app_user_id;

// Opção 2 — se o dado vem do nó imediatamente anterior
const userId = $input.first().json.app_user_id;

// Opção 3 — pegar todos os itens se for array
const userId = $('VarBD').all()[0].json.app_user_id;
```

---

**Ponto mais importante:** o nome dentro de `$('')` deve ser **exatamente igual** ao nome do nó no canvas, incluindo maiúsculas, espaços e acentos.

Por exemplo, se o nó se chama `Var BD` (com espaço):
```javascript
const userId = $('Var BD').first().json.app_user_id;
```


---

# 📊 Tabela Comparativa – Expressões Comuns

| Objetivo | Expressão | Recomendação Enterprise |
|----------|-----------|--------------------------|
| Fallback | `a || b || "default"` | ✔ Preferir OR |
| Concatenar simples | `a + " - " + b` | ✔ OK para simples |
| Concatenação entre nodes | `$('Node').item.json.a + "." + $('Node').item.json.b` | ✔ Usar quando dados vêm de nodes distintos |
| Último item | `array.at(-1)` | ✔ Preferir `.at()` |
| Split string | `split('.')` | ✔ Simples e eficiente |
| Remover não dígitos | `replace(/\D/g, '')` | ✔ Regex padrão |
| Optional chaining | `obj?.prop` | ✔ Sempre usar |
| Timestamp ms | `Date.now()` | ✔ IDs internos |
| Timestamp unix | `Math.floor(Date.now()/1000)` | ✔ APIs |
| ISO banco | `$now.toISO()` | ✔ Supabase/Postgres |

---

# 🧠 Snippets Essenciais

## 1️⃣ Fallback Seguro

```handlebars
{{ $json.primary || $json.backup || "N/A" }}
```

---

## 2️⃣ Concatenação Avançada Entre Nodes

```handlebars
{{ $('Webhook1').item.json.body.data.key.id + "." + $('Webhook1').item.json.body.data.message.documentMessage.title.split('.')[1] }}
```

---

## 3️⃣ Extração de Extensão

```handlebars
{{ $json.filename.split('.').at(-1) }}
```

---

## 4️⃣ Normalização de Telefone

```handlebars
{{ $json.phone?.replace(/\D/g, '') || "" }}
```

---

Aqui está o resumo técnico da solução para sua referência futura, focado na sanitização de dados para integração com APIs (como a Evolution API).

---

## 🛠️ Sanitização de String para APIs (n8n & Evolution)

Este código resolve problemas de **quebra de JSON** e **caracteres inválidos** ao enviar mensagens processadas por IAs através de requisições HTTP.

### 📝 A Expressão

```javascript
{{ $node["When Executed by Another Workflow"].json.text.replace(/["'\\“”‘’]/g, '').replace(/[\r\n]+/g, '\n').split('\n').map(s => s.trim()).filter(s => s) }}

```

### 🔍 O que cada parte faz (Regra de Pareto 80/20)

* **`.replace(/["'\\“”‘’]/g, '')`**: Remove os maiores "vilões" das APIs: aspas simples, duplas, barras invertidas e aspas tipográficas curvas. Isso impede que o JSON da requisição seja malformado.
* **`.replace(/[\r\n]+/g, '\n')`**: Normaliza quebras de linha múltiplas ou formatos diferentes (`\r\n`) em um único `\n`.
* **`.split('\n')`**: Transforma o bloco de texto em um **Array**, permitindo processar linha por linha.
* **`.map(s => s.trim())`**: Remove espaços inúteis no início e no fim de cada frase.
* **`.filter(s => s)`**: Remove linhas vazias do array final, garantindo que apenas conteúdo útil seja enviado.

---

## 🕒 Referência de Datas e Timestamps

| Objetivo | Expressão JavaScript | Exemplo |
| --- | --- | --- |
| **Unix Timestamp (10 dígitos)** | `{{ Math.floor(Date.now() / 1000) }}` | `1741143798` |
| **JS Timestamp (13 dígitos)** | `{{ Date.now() }}` | `1741143798000` |
| **Data ISO (Supabase)** | `{{ $now.toISO() }}` | `2026-03-15T...` |
| **Data PT-BR (WhatsApp)** | `{{ $now.setLocale('pt-br').toFormat('dd/MM/yyyy HH:mm') }}` | `15/03/2026 22:49` |

---

# 🕒 Datas e Timestamps (Modelo Pareto 80/20)

Você só precisa dominar 3 formatos:

## ✔ Referência de Datas e Timestamps

| Objetivo | Expressão JavaScript | Exemplo |
| --- | --- | --- |
| **Unix Timestamp (10 dígitos)** | `{{ Math.floor(Date.now() / 1000) }}` | `1741143798` |
| **JS Timestamp (13 dígitos)** | `{{ Date.now() }}` | `1741143798000` |
| **Data ISO (Supabase)** | `{{ $now.toISO() }}` | `2026-03-15T...` |
| **Data PT-BR (WhatsApp)** | `{{ $now.setLocale('pt-br').toFormat('dd/MM/yyyy HH:mm') }}` | `15/03/2026 22:49` |

---

# 🔁 Manipulação em Loop (Dica Sênior)

## Timestamp único por item

```handlebars
{{ Date.now() + $node["Split In Batches"].context.currentIteration }}
```

---

# 🔄 Conversão Timestamp → Data

```handlebars
{{ DateTime.fromMillis($json.timestamp).setLocale('pt-br').toFormat('dd/MM/yyyy') }}
```

---

# 🛡️ Governança Enterprise

✔ Sempre usar fallback  
✔ Sempre usar optional chaining  
✔ Evitar ternários aninhados  
✔ Evitar regex excessiva inline  
✔ Se lógica > 3 operações → usar Code Node  
✔ Padronizar nomes de nodes  

---

# 🚨 Anti‑Patterns

❌ Lógica complexa dentro de expressão  
❌ Dependência circular entre nodes  
❌ Falta de fallback  
❌ Manipulação de datas manualmente sem ISO  

---

# 📈 Modelo Mental Recomendado

Expressão n8n deve:

1. Ler
2. Transformar
3. Garantir fallback
4. Entregar valor seguro

Se precisar pensar muito → mover para Code Node.

---

# 🥶 Use esta abordagem para capturar o body real e debugar erro no code de inserção no supabase:

```js

  try {
    const raw = await this.helpers.httpRequest({
      method: 'POST',
      url: `${SUPABASE_URL}/rest/v1/menus_archives`,
      headers: {
        'Content-Type': 'application/json',
        'apikey': SUPABASE_KEY,
        'Authorization': `Bearer ${SUPABASE_KEY}`,
        'Prefer': 'return=representation'
      },
      body: { menu_id: menuId, file_url: fileUrl },
      json: true,
      returnFullResponse: true  // ← retorna status + body completo
    });
    console.log('✅ Status:', raw.statusCode);
    console.log('✅ Body:', JSON.stringify(raw.body));
  } catch (e) {
    console.log('❌ menu_id valor:', menuId);
    console.log('❌ file_url valor:', fileUrl);
    throw e;
  }

```

---

# 👤 Autor

Bruno Pelatieri Goulart  
Enterprise Automation Architect • AI • DevOps • n8n Specialist

---

© 2026 – Documentação técnica unificada.
