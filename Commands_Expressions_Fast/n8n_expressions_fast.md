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

## 📝 Extração de Dígitos e Sanitização

### 1. O Problema Original
O objetivo era extrair apenas os números de uma string de identificação (provavelmente um WhatsApp ID ou JID) localizada em uma estrutura JSON profunda, garantindo que o código não quebrasse caso o campo estivesse ausente.

**Estrutura do dado:** `$json.body.data.key.remoteJidAlt`
**Exemplo de entrada:** `"5511999998888@s.whatsapp.net"`
**Resultado desejado:** `"5511999998888"`

### 2. Análise da Solução Proposta
A versão otimizada utiliza recursos modernos do JavaScript (ES2020+) para substituir uma função anônima complexa por uma expressão de linha única.

#### **O Código Otimizado**
```javascript
$json.body.data.key.remoteJidAlt?.replace(/\D/g, '') ?? ""
```

### 3. Componentes da Solução (Glossário)

| Componente | Nome | Função |
| :--- | :--- | :--- |
| `?.` | **Optional Chaining** | Navega com segurança por objetos. Se `body` ou `data` não existirem, ele retorna `undefined` em vez de causar um erro de sistema. |
| `.replace()` | **Método String** | Substitui padrões dentro de uma string. |
| `/\D/g` | **Regex (Regular Expression)** | O padrão buscado. `\D` representa "qualquer caractere que **não** seja número". A flag `g` (global) garante que todos os caracteres sejam removidos, não apenas o primeiro. |
| `??` | **Nullish Coalescing** | Operador de fallback. Se o resultado da esquerda for `null` ou `undefined`, ele retorna o valor da direita (`""`). |

### 4. Por que usar `\D` em vez de `[0-9]`?
* `[0-9]` busca números.
* `\D` busca **o que não é número**.
* **Lógica:** Para "limpar" uma string e deixar só números, é mais eficiente dizer ao código: *"Pegue tudo que não for dígito e apague"* (substitua por vazio).

### 5. Boas Práticas Identificadas
1.  **Imutabilidade:** O código não altera o JSON original, apenas extrai o valor formatado.
2.  **Fail-safe:** O uso de `?? ""` evita que campos obrigatórios em etapas posteriores recebam valores nulos, o que costuma interromper fluxos de automação.
3.  **Performance:** A remoção da IIFE `(() => { ... })()` reduz o overhead de memória, processando a expressão diretamente.

### 6. A diferença fundamental

* **`||` (OR)**: Retorna o lado direito se o lado esquerdo for **qualquer valor falso** (`false`, `0`, `""`, `null`, `undefined`, `NaN`).
* **`??` (Nullish)**: Retorna o lado direito **apenas** se o lado esquerdo for `null` ou `undefined`.

### 7. Por que o `??` é mais seguro neste caso?

No seu código de limpeza de números, imagine que o resultado do `.replace()` seja uma string vazia (`""`).

* **Com `|| ""`**: O JavaScript vê a string vazia como "falsa" e tenta aplicar o fallback. No seu caso, o fallback também é `""`, então o resultado é o mesmo. Mas se o seu fallback fosse `"Não encontrado"`, o `||` substituiria uma string vazia (que é um resultado válido de limpeza) por `"Não encontrado"`.
* **Com `?? ""`**: Ele só aplicaria o fallback se o campo nem existisse. Se o campo existir mas não tiver números (resultado `""`), ele mantém a string vazia.

### 8. Tabela Comparativa de Comportamento

| Valor à Esquerda | Resultado com `|| "Padrão"` | Resultado com `?? "Padrão"` |
| :--- | :--- | :--- |
| `null` | `"Padrão"` | `"Padrão"` |
| `undefined` | `"Padrão"` | `"Padrão"` |
| `""` (String vazia) | **`"Padrão"`** | `""` |
| `0` | **`"Padrão"`** | `0` |
| `false` | **`"Padrão"`** | `false` |

### Resumo para sua pesquisa:
Use **`||`** quando você quer que **qualquer valor "vazio" ou "zero"** seja substituído.
Use **`??`** quando você quer apenas se prevenir contra **dados ausentes (nulos ou indefinidos)**, preservando strings vazias ou o número zero.

No contexto de automação (como o código que você enviou), o **`??`** é considerado a "boa prática moderna" porque é mais específico e evita substituições indesejadas em valores que são tecnicamente válidos, mas "falsos" para o JavaScript.

---

### Exemplo de Aplicação Prática
Se o campo contiver `"ID: 99-abc-123"`, o código retornará `"99123"`.

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

## 📱 Extração de Dígitos Finais (Sanitização de Telefone)

Esta expressão serve para isolar a parte invariável de um número de telefone, ignorando DDI (55), DDD e caracteres especiais.

### 📝 A Expressão
```javascript
{{ $json.whatsapp.replace(/\D/g, '').slice(-8) }}
```

### 🔍 Decomposição Técnica (Regra de Pareto 80/20)

1.  **`$json.whatsapp`**: Acessa o valor do campo vindo do nó anterior.
2.  **`.replace(/\D/g, '')`**: 
    * **`\D`**: Regex que significa "qualquer caractere que **NÃO** seja um dígito".
    * **`g`**: Flag global (substitui todos, não apenas o primeiro).
    * **`''`**: Substitui por nada.
    * *Resultado:* Transforma `+55 (19) 99249-6598` em `5519992496598`.
3.  **`.slice(-8)`**: 
    * O método `slice` corta a string. 
    * O valor **negativo** faz a contagem começar do **final para o início**.
    * *Resultado:* Pega apenas os últimos 8 números (`2496598`), ignorando se o número tem o "9" adicional, DDD ou DDI.

---

### ⚠️ Por que usar os últimos 8 dígitos?

* **Evita o erro do "9" Digito:** No Brasil, alguns sistemas enviam com o 9 na frente e outros não. Os últimos 8 dígitos são sempre os mesmos.
* **Independência de Formato:** Não importa se o usuário digitou com espaços, traços ou parênteses; a regex `\D` limpa tudo.
* **Busca no Banco:** É muito mais seguro buscar no **Supabase** usando os últimos 8 dígitos (operador `LIKE %valor`) do que tentar bater o número completo, que pode variar entre `55`, `055`, ou sem DDI.

### Dica Sênior para o Erro de Importação
Se você for usar essa expressão em um novo nó e o n8n der o erro *"Could not find property option"*, lembre-se: crie um nó **Set** ou **Edit Fields** do zero e cole apenas a expressão acima. Não tente importar o nó de outro workflow via arquivo JSON.

---

# 🕒 Datas e Timestamps (Modelo Pareto 80/20)

Para inserir esse timestamp no **Supabase**, você precisa primeiro identificar qual é o tipo de dado da sua coluna no banco. O número `1773963828` é um **Unix Timestamp em segundos** (referente a abril de 2026).

Aqui estão as duas formas de ajustar isso via JavaScript no nó **Code** do n8n:

### 1. Se a coluna for `timestamp` ou `timestamptz` (Recomendado)
O Supabase (PostgreSQL) prefere o formato ISO8601. Você deve converter os segundos em milissegundos e gerar a string de data.

```javascript
const unixSegundos = 1773963828;
const dataISO = new Date(unixSegundos * 1000).toISOString();

return {
  json: {
    data_para_supabase: dataISO // Resultado: "2026-04-22T23:43:48.000Z"
  }
};
```

### 2. Se a coluna for `int8` ou `numeric`
Se você definiu a coluna como um número inteiro para economizar processamento, não precisa de ajuste de formato, apenas garanta que o JavaScript trate como número:

```javascript
return {
  json: {
    data_para_supabase: Number(1773963828)
  }
};
```

### 🕒 Resumo Técnico: Datas e Timestamps (Pareto 80/20)

Você só precisa dominar 3 formatos:

| Objetivo | Expressão JavaScript | Exemplo |
| --- | --- | --- |
| **Unix Timestamp (10 dígitos)** | `{{ Math.floor(Date.now() / 1000) }}` | `1741143798` |
| **JS Timestamp (13 dígitos)** | `{{ Date.now() }}` | `1741143798000` |
| **Data ISO (Supabase)** | `{{ $now.toISO() }}` | `2026-03-15T...` |
| **Data PT-BR (WhatsApp)** | `{{ $now.setLocale('pt-br').toFormat('dd/MM/yyyy HH:mm') }}` | `15/03/2026 22:49` |
| **Unix para ISO (Banco)** | `new Date(ts * 1000).toISOString()` | `2026-04-22T23:43:48Z` |

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
