Aqui está a sua "Folha de Colas" (Cheat Sheet) definitiva para operadores do Supabase (PostgREST) via requisições GET. Esta lista foi otimizada para consulta rápida no desenvolvimento.

---

## 🚀 Guia de Operadores Supabase (GET / PostgREST)

### 1. Operadores de Comparação Básica
Estes são os mais usados para filtros simples (`where`).

| Operador | Significado | Exemplo na URL |
| :--- | :--- | :--- |
| `eq` | Igual a (Equal) | `?coluna=eq.valor` |
| `neq` | Diferente de (Not Equal) | `?coluna=neq.valor` |
| `gt` | Maior que (Greater Than) | `?coluna=gt.10` |
| `gte` | Maior ou igual (Greater Than or Equal) | `?coluna=gte.10` |
| `lt` | Menor que (Less Than) | `?coluna=lt.5` |
| `lte` | Menor ou igual (Less Than or Equal) | `?coluna=lte.5` |
| `in` | Está na lista (Array) | `?coluna=in.(valor1,valor2)` |
| `is` | Verificação de Nulidade/Booleano | `?coluna=is.null` ou `?coluna=is.true` |

---

### 2. Operadores de Texto (Pattern Matching)
Essenciais para buscas por nome ou partes de strings.

| Operador | Significado | Exemplo na URL |
| :--- | :--- | :--- |
| `like` | Busca com padrão (Case-sensitive) | `?nome=like.*Silva*` |
| `ilike` | Busca com padrão (**Case-insensitive**) | `?nome=ilike.*silva*` |
| `fts` | Full-Text Search (Busca avançada) | `?coluna=fts.termo` |
| `not.like` | Não corresponde ao padrão | `?nome=not.like.*Teste*` |

> **Dica Sênior:** O asterisco (`*`) funciona como o `%` do SQL. Use `*termo*` para "contém", `termo*` para "começa com" e `*termo` para "termina com".

---

### 3. Operadores de Lógica e Filtros Avançados
Para quando você precisa de mais de uma condição.

| Operador | Significado | Exemplo na URL |
| :--- | :--- | :--- |
| **AND** | E (Padrão ao usar `&`) | `?col1=eq.A&col2=eq.B` |
| **OR** | Ou | `?or=(col1.eq.A,col2.eq.B)` |
| **NOT** | Negação do operador | `?coluna=not.eq.valor` |
| **Nulo** | Campo vazio | `?coluna=is.null` |
| **Não Nulo** | Campo com valor | `?coluna=not.is.null` |

---

### 4. Seleção e Joins (O "Coração" do GET)
Controlam **o que** e **como** os dados retornam.

| Parâmetro | Função | Exemplo na URL |
| :--- | :--- | :--- |
| `select` | Escolher colunas específicas | `?select=id,nome,data` |
| `!inner` | Forçar Inner Join | `?select=*,tabela_rel!inner(*)` |
| `order` | Ordenação (asc/desc) | `?order=created_at.desc` |
| `limit` | Paginação (Limite de linhas) | `?limit=10` |
| `offset` | Pular linhas (Paginação) | `?offset=20` |

---

### 🛠️ A Sintaxe Correta

**Como você escreveu:** `valor=neq.(whatsapp,assistent)` ❌
**Como deve ser:** `valor=not.in.(whatsapp,assistent)` ✅

---

### 🔍 Por que usar o `not.in`?

1.  **`neq`**: Espera apenas um argumento. Se você passar parênteses com vírgula, o Supabase vai procurar uma string literal que contenha exatamente os parênteses e a vírgula, o que provavelmente resultará em zero dados.
2.  **`not.in`**: Foi desenhado justamente para arrays/listas. Ele diz ao banco: "Me traga tudo, EXCETO o que estiver dentro desses parênteses".

---

### 🕒 Resumo Técnico: Operadores de Exclusão (Pareto 80/20)

| Objetivo | Operador PostgREST | Exemplo na URL |
| :--- | :--- | :--- |
| **Diferente de UM** | `neq` | `&type=neq.2` |
| **Diferente de VÁRIOS** | `not.in` | `&source=not.in.(bot,system)` |
| **Não é Nulo** | `not.is.null` | `&body=not.is.null` |
| **Não contém texto** | `not.ilike` | `&text=not.ilike.*spam*` |

---

### ➕ Adição Bônus: Operadores de Datas e Intervalos
Como seu app trabalha com agendamentos (`rob_set_schedule`), você vai precisar filtrar por períodos com frequência.

| Operador | Significado | Exemplo na URL |
| :--- | :--- | :--- |
| `ov` | Sobreposição (Overlap) | `?periodo=ov.[2026-01-01,2026-01-31]` |
| `sl` | Estritamente à esquerda (Antes de) | `?data=sl.2026-03-28` |
| `sr` | Estritamente à direita (Depois de) | `?data=sr.2026-03-28` |

---

### 🛠️ Dica de Ouro para o n8n: Encoding de Caracteres
Às vezes, ao usar o `OR` ou o `NOT IN`, a URL pode conter espaços ou caracteres especiais que quebram o nó HTTP. 

> **Regra Sênior:** Se a sua consulta falhar mesmo parecendo correta, use a função do n8n para garantir que a URL seja lida corretamente pela API:
> `{{ encodeURIComponent("not.in.(whatsapp,assistent)") }}`

---

### 🕒 Resumo Técnico de Performance (Pareto 80/20)

Para manter o seu banco Supabase saudável enquanto o app cresce:

1.  **Sempre use `limit`:** Nunca peça todos os dados se você só precisa do último.
2.  **Selecione colunas:** O `select=id,content` é muito mais rápido que o `select=*`.
3.  **Índices:** No Supabase, certifique-se de que colunas usadas no `order` (como `created_at`) ou filtros frequentes (como `user_id`) tenham um **Index** criado.

---

## ⏳ Guia de Manipulação de Datas (n8n ↔️ Supabase)

### 1. Formatos e Conceitos de Geração
Use estas expressões dentro dos campos `Value` ou na `URL` do seu nó HTTP.

| Conceito | Uso Principal | Expressão no n8n (JavaScript/Luxon) | Exemplo de Saída |
| :--- | :--- | :--- | :--- |
| **Unix Timestamp** | IDs de log e cálculos rápidos | `{{ Math.floor(Date.now() / 1000) }}` | `1743124540` |
| **ISO 8601 (Date)** | Filtros de dia (Calendário) | `{{ $now.toISODate() }}` | `2026-03-28` |
| **ISO 8601 (Full)** | Precisão de milissegundos | `{{ $now.toISO() }}` | `2026-03-28T00:42:20Z` |
| **Dia da Semana** | Agendamentos (`rob_set`) | `{{ $now.weekday }}` | `6` (Sábado) |

---

### 2. Operadores de Filtro na URL (GET)
Combine os operadores da sua lista anterior com as datas geradas acima.

| Objetivo | Operador | Exemplo de URL no n8n |
| :--- | :--- | :--- |
| **Exatamente hoje** | `eq` | `?data=eq.{{ $now.toISODate() }}` |
| **Criado após as 08h** | `gt` | `?created_at=gt.2026-03-28T08:00:00` |
| **Antes de agora** | `lt` | `?created_at=lt.{{ $now.toISO() }}` |
| **Entre duas datas** | `and` | `?data=gte.2026-03-01&data=lte.2026-03-31` |
| **Não é hoje** | `neq` | `?data=neq.{{ $now.toISODate() }}` |

---

### 3. Cálculos de Tempo (Luxon)
Você pode precisar buscar algo que aconteceu "há X minutos".

| Cenário | Expressão n8n | Resultado Esperado |
| :--- | :--- | :--- |
| **Há 15 minutos** | `{{ $now.minus({ minutes: 15 }).toISO() }}` | Filtra mensagens recentes. |
| **Início do dia** | `{{ $now.startOf('day').toISO() }}` | Pega tudo de "hoje". |
| **Final do dia** | `{{ $now.endOf('day').toISO() }}` | Útil para prazos de entrega. |
| **Próxima segunda** | `{{ $now.plus({ weeks: 1 }).set({ weekday: 1 }).toISODate() }}` | Próximo ciclo de KPIs. |

---

### 🕒 Resumo Técnico: O "Pulo do Gato" (Pareto 80/20)

*   **Timestamp vs Date:** Se a sua coluna no Supabase for do tipo `timestamptz`, prefira usar o formato **ISO** (`.toISO()`). Se for uma coluna de número simples (integer), use o **Unix Timestamp** (`Math.floor...`).
*   **Timezone:** O Supabase armazena tudo em **UTC**. Como você está no Brasil (UTC-3), o `$now` do n8n geralmente já compensa isso, mas se as horas "não baterem", use `.toUTC()` na sua expressão do n8n antes de enviar.

---

### ⚠️ Dica de Sênior: Verificação de "Atraso"
Se você quiser que o seu robô verifique se uma mensagem está "parada" há mais de 10 minutos no banco:
`?created_at=lt.{{ $now.minus({ minutes: 10 }).toISO() }}&status=eq.pendente`

---