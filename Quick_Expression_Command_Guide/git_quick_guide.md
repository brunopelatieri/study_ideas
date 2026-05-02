# 📚 Git — Guia de Referência e Base de Conhecimento

> Guia prático com os comandos Git mais utilizados, ordenados por frequência de uso, incluindo casos reais de uso no dia a dia de desenvolvimento.

---

## Sumário

- [Comandos Essenciais](#-comandos-essenciais)
- [Branches](#-branches)
- [Atualização e Sincronização](#-atualização-e-sincronização)
- [Rebase e Merge](#-rebase-e-merge)
- [Desfazendo Alterações](#-desfazendo-alterações)
- [Inspeção e Diagnóstico](#-inspeção-e-diagnóstico)
- [Stash](#-stash)
- [Tags](#-tags)
- [Configuração](#-configuração)
- [Casos de Uso Reais](#-casos-de-uso-reais)

---

## 🔴 Comandos Essenciais

Os mais usados no dia a dia, praticamente em todo projeto.

### `git status`
Mostra o estado atual do repositório: arquivos modificados, staged e não rastreados.
```bash
git status
```

### `git add`
Adiciona arquivos para a área de staging (preparando para o commit).
```bash
git add .                        # todos os arquivos
git add src/arquivo.tsx          # arquivo específico
git add src/components/          # pasta inteira
```

### `git commit`
Cria um snapshot do que está em staging com uma mensagem descritiva.
```bash
git commit -m "descrição clara do que foi feito"
git commit -a -m "add e commit ao mesmo tempo (arquivos já rastreados)"
```

> ✅ **Boas práticas de mensagem:**
> - Use verbos no imperativo: `fix`, `add`, `update`, `remove`
> - Seja específico: `fix login redirect on mobile` em vez de `fix bug`

### `git push`
Envia seus commits locais para o repositório remoto.
```bash
git push                                    # push na branch atual
git push origin nome-da-branch              # push em branch específica
git push origin nome-da-branch --force-with-lease  # push após rebase (seguro)
```

> ⚠️ Prefira sempre `--force-with-lease` em vez de `--force`. Ele verifica se ninguém mais alterou a branch antes de sobrescrever.

### `git pull`
Baixa e aplica as atualizações do remoto na branch atual.
```bash
git pull                         # pull da branch atual
git pull origin develop          # pull de uma branch específica
```

---

## 🌿 Branches

### `git branch`
Lista, cria ou deleta branches.
```bash
git branch                       # lista branches locais
git branch -a                    # lista branches locais e remotas
git branch nome-da-branch        # cria nova branch
git branch -d nome-da-branch     # deleta branch (seguro)
git branch -D nome-da-branch     # deleta branch (forçado)
```

### `git checkout`
Navega entre branches ou restaura arquivos.
```bash
git checkout nome-da-branch      # muda para branch existente
git checkout -b nome-da-branch   # cria e muda para nova branch
git checkout -b nova develop     # cria branch a partir da develop
git checkout -- arquivo.tsx      # descarta alterações de um arquivo
```

### `git switch` *(mais moderno que checkout)*
```bash
git switch nome-da-branch        # muda para branch existente
git switch -c nome-da-branch     # cria e muda para nova branch
```

---

## 🔄 Atualização e Sincronização

### `git fetch`
Baixa atualizações do remoto **sem aplicar** no código local.
```bash
git fetch origin                 # busca atualizações do remoto
git fetch -p                     # busca + remove referências de branches deletadas no remoto
git fetch --prune                # mesmo que -p
```

> 💡 `git fetch -p` é um bom hábito para manter sua lista de branches limpa.

### `git remote`
Gerencia conexões com repositórios remotos.
```bash
git remote -v                    # lista os remotos configurados
git remote add origin URL        # adiciona um remoto
git remote remove origin         # remove um remoto
```

---

## 🔀 Rebase e Merge

### `git merge`
Une duas branches preservando o histórico completo com um merge commit.
```bash
git checkout develop
git merge feature/minha-feature  # traz as alterações para develop
```

> Use merge quando a branch é compartilhada com outros desenvolvedores.

### `git rebase`
Reaplica commits em cima de outra branch, criando um histórico linear e limpo.
```bash
git checkout minha-branch
git rebase develop               # reaplica commits em cima da develop
git rebase outra-branch          # reaplica commits em cima de outra branch
```

> Use rebase quando a branch é sua e não é compartilhada.

### Resolvendo conflitos durante rebase/merge
```bash
# 1. Resolva os conflitos nos arquivos indicados
# 2. Adicione os arquivos resolvidos
git add arquivo-resolvido.tsx

# 3. Continue
git rebase --continue            # continua o rebase
git merge --continue             # continua o merge

# Para abortar e voltar ao estado anterior
git rebase --abort
git merge --abort
```

### Indicadores de conflito no arquivo
```
<<<<<<< HEAD
  (seu código — Alteração Atual)
=======
  (código da outra branch — Alteração de Entrada)
>>>>>>> nome-da-branch
```

| Opção no VS Code | Quando usar |
|---|---|
| Aceitar Alteração Atual | Manter apenas seu código |
| Aceitar Alteração de Entrada | Manter apenas o código da outra branch |
| Aceitar Ambas | Quando os dois lados precisam existir |
| Comparar Alterações | Para analisar antes de decidir |

---

## ↩️ Desfazendo Alterações

### `git restore`
Descarta alterações não commitadas.
```bash
git restore arquivo.tsx          # descarta alterações de um arquivo
git restore .                    # descarta todas as alterações locais
git restore --staged arquivo.tsx # remove arquivo do staging (mantém alterações)
```

### `git reset`
Move o ponteiro HEAD para um commit anterior.
```bash
git reset HEAD~1                 # desfaz último commit (mantém alterações)
git reset --soft HEAD~1          # desfaz commit, mantém tudo em staging
git reset --hard HEAD~1          # desfaz commit E descarta alterações ⚠️
git reset --hard origin/branch   # iguala branch local ao remoto ⚠️
```

> ⚠️ `--hard` é destrutivo — alterações são perdidas sem recuperação.

### `git revert`
Cria um novo commit que desfaz um commit anterior (seguro para branches compartilhadas).
```bash
git revert abc1234               # reverte um commit pelo hash
git revert HEAD                  # reverte o último commit
```

---

## 🔍 Inspeção e Diagnóstico

### `git log`
Exibe o histórico de commits.
```bash
git log                          # histórico completo
git log --oneline                # resumido, uma linha por commit
git log --oneline --graph        # com gráfico de branches
git log --oneline -10            # últimos 10 commits
git log --author="Nome"          # commits de um autor
git log arquivo.tsx              # histórico de um arquivo
```

### `git diff`
Mostra as diferenças entre versões.
```bash
git diff                         # alterações não staged
git diff --staged                # alterações em staging
git diff branch1 branch2         # diferença entre duas branches
git diff abc1234 def5678         # diferença entre dois commits
```

### `git show`
Mostra detalhes de um commit específico.
```bash
git show                         # detalhes do último commit
git show abc1234                 # detalhes de um commit específico
```

### `git blame`
Mostra quem alterou cada linha de um arquivo.
```bash
git blame arquivo.tsx
```

---

## 📦 Stash

Salva alterações temporariamente sem precisar commitar.

```bash
git stash                        # salva alterações atuais
git stash save "descrição"       # salva com descrição
git stash list                   # lista todos os stashes
git stash pop                    # aplica o último stash e remove da lista
git stash apply                  # aplica o último stash e mantém na lista
git stash apply stash@{2}        # aplica um stash específico
git stash drop stash@{0}         # remove um stash específico
git stash clear                  # remove todos os stashes
```

> 💡 Útil quando precisa trocar de branch mas não quer commitar algo incompleto.

---

## 🏷️ Tags

Marcam versões/releases específicas no histórico.

```bash
git tag                          # lista todas as tags
git tag v1.0.0                   # cria tag leve
git tag -a v1.0.0 -m "Release"  # cria tag anotada (recomendado)
git push origin v1.0.0           # envia tag para o remoto
git push origin --tags           # envia todas as tags
git tag -d v1.0.0                # deleta tag local
```

---

## ⚙️ Configuração

```bash
git config --global user.name "Seu Nome"
git config --global user.email "seu@email.com"
git config --global core.editor "code --wait"   # VS Code como editor padrão
git config --list                                 # lista todas as configurações
```

---

## 💼 Casos de Uso Reais

### Caso 1 — Trazer alterações de outra branch para a minha (abordado neste guia)

**Cenário:** Você está na `adm_training` e precisa das alterações que fez na `adjust_team`.

```bash
# A branch adjust_team foi criada a partir da develop atualizada
git checkout adm_training
git rebase adjust_team

# Se já tinha feito push antes, precisa forçar
git push origin adm_training --force-with-lease
```

> ✅ Use rebase quando a branch é só sua — mantém histórico linear e facilita o merge do outro dev na develop.

---

### Caso 2 — Outro desenvolvedor precisa atualizar após seu force push

Quando você faz `--force-with-lease`, quem já tinha baixado sua branch precisa sincronizar:

```bash
git fetch origin
git checkout adm_training
git reset --hard origin/adm_training
```

> ⚠️ O `reset --hard` descarta os commits locais e iguala ao remoto. Só use se tiver certeza que não há trabalho local a preservar.

---

### Caso 3 — Resolver conflitos de rebase com arquivo M (Modified)

Ao fazer rebase, arquivos marcados com **M** (Modified) precisam de revisão manual.

```bash
# Verificar o conflito
git diff src/components/MeuArquivo.tsx

# Após resolver manualmente no VS Code
git add src/components/MeuArquivo.tsx
git rebase --continue
```

**Indicadores no VS Code:**
- `A` — arquivo novo (Added) → sem conflito, aceito automaticamente
- `M` — arquivo modificado (Modified) → pode ter conflito, revisar manualmente

---

### Caso 4 — Iniciar um projeto do zero com repositório remoto

```bash
git init
git add .
git commit -m "first commit"
git remote add origin https://github.com/usuario/repositorio.git
git push -u origin main
```

---

### Caso 5 — Clonar e criar branch de feature

```bash
git clone https://github.com/usuario/repositorio.git
cd repositorio
git checkout develop
git pull
git checkout -b feature/minha-feature
```

---

### Caso 6 — Salvar trabalho incompleto e trocar de branch

```bash
git stash save "wip: ajustes no formulário"
git checkout outra-branch

# Quando voltar
git checkout minha-branch
git stash pop
```

---

### Caso 7 — Limpar branches remotas deletadas

```bash
git fetch -p
# ou
git fetch --prune
```

> Remove referências locais de branches que já foram deletadas no servidor remoto.

---

### Caso 8 — Verificar quais funções são exportadas em um arquivo

```bash
grep "export function\|export const" src/hooks/useTeamMembers.ts
```

---

### Caso 9 — Corrigir mensagem do último commit

```bash
git commit --amend -m "nova mensagem correta"
# Se já fez push:
git push origin minha-branch --force-with-lease
```

---

### Caso 10 — Fluxo completo de trabalho (dia a dia)

```bash
# 1. Atualiza a develop
git checkout develop
git pull

# 2. Cria branch de feature
git checkout -b feature/nova-funcionalidade

# 3. Desenvolve... adiciona e commita
git add .
git commit -m "feat: adiciona nova funcionalidade"

# 4. Atualiza com a develop antes de abrir PR
git rebase develop

# 5. Envia para o remoto
git push origin feature/nova-funcionalidade

# 6. Abre Pull Request no GitHub
```

---

## 📋 Referência Rápida — Tabela de Comandos

| Comando | O que faz |
|---|---|
| `git status` | Estado atual do repositório |
| `git add .` | Adiciona tudo ao staging |
| `git commit -m ""` | Cria um commit |
| `git push` | Envia commits para o remoto |
| `git pull` | Baixa e aplica atualizações |
| `git fetch -p` | Baixa atualizações sem aplicar + limpa branches deletadas |
| `git checkout -b` | Cria e muda para nova branch |
| `git merge` | Une branches (com merge commit) |
| `git rebase` | Reaplica commits (histórico linear) |
| `git stash` | Salva alterações temporariamente |
| `git log --oneline` | Histórico resumido |
| `git diff` | Mostra diferenças |
| `git reset --hard` | Descarta tudo e volta a um ponto ⚠️ |
| `git revert` | Desfaz commit de forma segura |
| `git tag` | Marca versões/releases |
| `git rebase --abort` | Cancela rebase em andamento |
| `git rebase --continue` | Continua rebase após resolver conflito |

---

> 📝 **Guia criado com base em situações reais de desenvolvimento.**
> Documento atualizado conforme novos casos surgirem.

---

# 👤 Autor

Bruno Pelatieri Goulart  
Enterprise Automation Architect • AI • DevOps • n8n Specialist

---


