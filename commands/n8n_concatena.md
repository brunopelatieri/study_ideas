### 💡 Resumo Técnico: Datas e Timestamps (Pareto 80/20)

> **O Princípio de Pareto aplicado:** Em integrações de API (Supabase/Gemini), você não precisa dominar todo o objeto `Date`. Você só precisa de: **ISO** para o banco, **Unix** para cálculos/IDs e **Formatada** para o usuário final.

---

### 📝 Conteúdo para Copiar e Colar (Markdown)

```markdown
# 🛠️ Guia de Referência: Datas em JavaScript (n8n)

## 1. Timestamps (Números Inteiros)
*Essenciais para IDs únicos, ordenação em bancos de dados e cálculos de performance.*

- **Milissegundos (13 dígitos):** Ideal para IDs internos do JavaScript.
  ```javascript
  // No nó Code
  const tsMs = Date.now(); 
  // Na Expressão do n8n
  {{ Date.now() }}

```

* **Segundos (Unix Standard - 10 dígitos):** Padrão de APIs e do Supabase.
```javascript
// No nó Code
const tsUnix = Math.floor(Date.now() / 1000);
// Na Expressão do n8n
{{ Math.floor(Date.now() / 1000) }}

```



## 2. Formatação PT-BR (Brasileira)

*Use apenas na saída final para o usuário (ex: Mensagem de confirmação no WhatsApp).*

* **Data Curta (DD/MM/AAAA):**
```javascript
{{ $now.setLocale('pt-br').toFormat('dd/MM/yyyy') }}

```


* **Data e Hora Completa:**
```javascript
{{ $now.setLocale('pt-br').toFormat('dd/MM/yyyy HH:mm:ss') }}

```



## 3. Formato para Banco de Dados (ISO 8601)

*O único formato universal aceito sem erros por quase todos os bancos (Supabase, PostgreSQL).*

* **ISO String:**
```javascript
{{ $now.toISO() }} 
// Resultado: 2026-02-24T19:30:00.000Z

```



## 4. Manipulação em Loops (Dica Sênior)

Ao salvar itens dentro de um loop (como ingredientes), para garantir que o timestamp seja ligeiramente diferente ou fixo para o lote:

* **Fixar o mesmo tempo para todos no lote:**
Referencie o tempo do nó de entrada em vez de gerar um novo.
* **Timestamp + Index do Loop (ID Único):**
```javascript
{{ Date.now() + $node["Split In Batches"].context.currentIteration }}

```



## 5. Conversão (Timestamp Int ➔ Data PT-BR)

Se você já tem um número inteiro e quer mostrar como data:

```javascript
{{ DateTime.fromMillis($json.timestamp_inteiro).setLocale('pt-br').toFormat('dd/MM/yyyy') }}

```

```

---



### Próximo Passo
Agora que as datas estão padronizadas, **você gostaria que eu criasse a expressão final para o seu nó de resposta do WhatsApp, já unindo o nome do ingrediente com a data de processamento formatada?**

```