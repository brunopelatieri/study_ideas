# 📘 Guia Definitivo: Upsert Supabase no n8n

Este guia detalha como inserir ou atualizar dados no Supabase usando o nó **HTTP Request**, garantindo que funcione de forma resiliente.

## 1. O Conceito de Upsert
O **Upsert** (Update + Insert) permite que você envie dados para o banco sem se preocupar se o registro já existe.
* **Se não existe:** Cria um novo registro.
* **Se existe:** Atualiza as colunas enviadas.
* **Requisito:** A tabela deve ter uma coluna **UNIQUE** (Ex: `id`, `phone` ou `email`).

---

## 2. Configuração do Nó HTTP Request

### 🌐 URL da Requisição
A URL deve apontar para a sua tabela e incluir o parâmetro de conflito na coluna única.
> `https://[SEU_ID].supabase.co/rest/v1/[NOME_DA_TABELA]?on_conflict=[COLUNA_UNICA]`

**Exemplo Prático:**
`https://[SEU_ID].supabase.co/rest/v1/team_members?on_conflict=phone`

### 🔑 Autenticação e Headers (Ajustados)
A autenticação exige dois tokens e o header de preferência para ativar o Upsert.

| Header | Valor | Observação |
| :--- | :--- | :--- |
| **Content-Type** | `application/json` | Padrão para envio de dados. |
| **apikey** | `{{Sua_Anon_Key}}` | Chave pública do Supabase. |
| **Authorization** | `Bearer {{Sua_Anon_Key}}` | **Atenção:** Deve haver um espaço após a palavra `Bearer`. |
| **Prefer** | `resolution=merge-duplicates` | **CRÍTICO:** É o que transforma o POST em Upsert. |
| **Prefer** | `return=representation` | (Opcional) Retorna o dado inserido no output do nó. |

---

## 📦 3. Estrutura do Body (JSON)

Para evitar o erro **[ERROR: Reference]**, nunca use `$json` puro se o dado vier de nós distantes. Use a referência absoluta pelo **nome do nó**.

**Exemplo de JSON Seguro:**
```json
{
  "name": "{{ $('VarBD').item.json.app_user_name }}",
  "role": "{{ $('VarBD').item.json.app_user_role }}",
  "phone": "{{ $('VarBD').item.json.app_user_phone }}"
}
```
*Dica: Arraste os campos da aba **Nodes** no editor de expressões para que o n8n escreva o nome do nó automaticamente.*

---

## ⚠️ 4. Checkpoint de Erros Comuns

1.  **Erro 401 (Unauthorized):** Verifique se há um espaço entre `Bearer` e a chave no header `Authorization`.
2.  **Erro 409 (Conflict):** Significa que você esqueceu o header `Prefer: resolution=merge-duplicates`. O Supabase tentou inserir um dado que já existe e o banco bloqueou.
3.  **Erro 404 (Not Found):** Verifique se o nome da tabela na URL está escrito exatamente como no banco de dados.
4.  **ReferenceError:** O nó de origem (ex: `VarBD`) não está conectado ao nó `HTTP Request` por uma linha no canvas. **Sem fio = Sem dados.**

---

## 💡 5. Resumo de Comandos Rápidos para Expressões

* **Ternário (Se existir nome, use; se não, vazio):**
    `{{ $('VarBD').item.json.name ? $('VarBD').item.json.name : 'Cliente' }}`
* **Limpeza de Resposta de IA (Para o nó Code):**
    ```javascript
    const clean = $json.output.replace(/```json|```/g, "").trim();
    return { json: JSON.parse(clean) };
    ```

---
**Dica Final:** Sempre execute o nó de origem (`VarBD`) antes de configurar o `HTTP Request`. Isso faz com que os nomes dos campos apareçam na aba **Nodes**, facilitando o arraste sem erros.