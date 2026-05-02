## 🚀 Guia de Sobrevivência gcloud CLI

Siga esta sequência lógica para configurar seu ambiente, gerenciar servidores e navegar entre o Windows e o Linux.

1. Instalação do Binário
Download: É necessário baixar o instalador oficial para Windows.

Caminho Padrão: Ele geralmente se instala em C:\Program Files (x86)\Google\Cloud SDK, que é o diretório que apareceu nos seus prints recentes.

2. Preparação do Terminal
Variáveis de Ambiente: Durante a instalação, existe uma opção para "Add google-cloud-sdk to system PATH". Isso é fundamental para que você consiga digitar gcloud em qualquer pasta do terminal sem receber erro de "comando não encontrado".

3. O "Vínculo" (Link)
Primeiro Login: Logo após instalar, o comando de abertura é o gcloud init. É ele que faz a ponte entre o software instalado no seu PC e a sua conta do Google no navegador.

Dica para o seu guia:
Se você for usar o Cursor IDE para desenvolver o DiretorIA, vale mencionar que você pode rodar esses mesmos comandos no terminal embutido do Cursor (Ctrl + '), facilitando o deploy enquanto você codifica.

---

### 1. Inicialização e Autenticação
O primeiro passo para conectar o seu terminal local à sua infraestrutura na nuvem.

*   **Comando:** `gcloud init`
*   **O que faz:** Abre o navegador para login, permite selecionar seu projeto e define a zona/região padrão.
*   **Uso Prático:**
    > Execute este comando sempre que trocar de conta ou precisar reconfigurar o projeto padrão do zero.

---

### 2. Diagnóstico de Ambiente
Antes de agir, é preciso saber quem você é e onde está trabalhando dentro do GCP.

*   **Ver conta ativa:** `gcloud auth list`
*   **Ver configurações (Projeto e Zona):** `gcloud config list`
*   **Trocar de projeto rapidamente:** 
    `gcloud config set project [ID-DO-SEU-PROJETO]`

---

### 3. Gestão de Instâncias (VMs)
Aqui você gerencia a vida dos seus servidores. Com base nos seus projetos, como o **DiretorIA**, esses comandos são essenciais para controle de custos e manutenção.

#### A. Listar Servidores
*   **Comando:** `gcloud compute instances list`
*   **Importância:** Mostra o **NAME**, **ZONE** (ex: `us-central1-c`) e o **STATUS** (se está `RUNNING` ou `TERMINATED`).

#### B. Ligar/Desligar Máquinas
*   **Iniciar:** `gcloud compute instances start [NOME-DA-VM] --zone=[ZONA-DA-VM]`
*   **Parar:** `gcloud compute instances stop [NOME-DA-VM] --zone=[ZONA-DA-VM]`
    *   *Exemplo:* `gcloud compute instances start n8n-vm-v0 --zone=southamerica-east1-b`

---

### 4. Acesso Remoto (Entrando no Linux)
O comando mais utilizado para realizar deploy ou manutenção em ferramentas como **n8n** ou **Docker**.

*   **Comando:** `gcloud compute ssh [NOME-DA-VM] --zone=[ZONA-DA-VM]`
*   **O que acontece:** O terminal do Windows se transforma no terminal do servidor Linux.
*   **Dica de Saída:** Para encerrar a conexão e voltar ao Windows, digite `exit`.

---

### 5. Transferência de Arquivos
Essencial para mover arquivos de configuração ou códigos entre seu computador e o servidor.

*   **Do Windows para o Linux:**
    `gcloud compute scp [CAMINHO-ARQUIVO-LOCAL] [NOME-DA-VM]:[DESTINO-NO-LINUX] --zone=[ZONA-DA-VM]`
*   **Do Linux para o Windows:**
    `gcloud compute scp [NOME-DA-VM]:[CAMINHO-NO-LINUX] [DESTINO-NO-WINDOWS] --zone=[ZONA-DA-VM]`

---

## 🛠️ Tabela de Referência Rápida

| Ação | Comando gcloud |
| :--- | :--- |
| **Configuração Inicial** | `gcloud init` |
| **Verificar Status da VM** | `gcloud compute instances list` |
| **Ligar VM Desligada** | `gcloud compute instances start [NOME]` |
| **Acessar Terminal Linux** | `gcloud compute ssh [NOME] --zone=[ZONA]` |
| **Sair do Linux** | `exit` |

---

### 💡 Dicas de Ouro para Desenvolvedores
1.  **Status TERMINATED:** Se tentar o SSH e der erro "Resource not found", verifique se a máquina está ligada e se a **Zona** no comando é a mesma que aparece no `list`.
2.  **Automação:** Como você utiliza **n8n** e **Docker**, prefira sempre fixar a zona no comando para evitar que o gcloud pergunte toda vez.
3.  **Prompt Engineering:** Ao pedir ajuda para scripts complexos de automação, continue usando as tags `<context>` e `<task>` para manter a precisão dos modelos.

---

## Guia de Conexão e Transferência: Google Cloud GCP para IDEs

Este guia ensina como configurar o ambiente para gerenciar instâncias do Google Cloud, transferir arquivos e conectar as IDEs **Cursor** ou **VSCode** via SSH.

### 1. Preparação: Configurando o Google Cloud SDK
O primeiro passo é garantir que o terminal local esteja autenticado no projeto correto.

1.  **Inicie a configuração:** No terminal (SDK Shell), execute `gcloud init`.
2.  **Selecione o Projeto:** Escolha o ID do projeto onde sua instância está hospedada.
3.  **Identifique a Instância:** Use o comando abaixo para localizar sua máquina ativa e a respectiva zona:
    ```bash
    gcloud compute instances list
    ```

---

### 2. Como baixar pastas e arquivos (Via Terminal Local)
Para transferir conteúdos da nuvem para sua máquina local, utilize o utilitário `scp` do gcloud. Use variáveis para os caminhos de acordo com sua necessidade.

**Comando para baixar uma pasta completa:**
```bash
gcloud compute scp --recurse [USUARIO_REMOTO]@[NOME_DA_INSTANCIA]:[CAMINHO_PASTA_REMOTA] "[CAMINHO_PASTA_LOCAL]" --zone=[ZONA_DA_INSTANCIA]
```

**Exemplo de preenchimento:**
*   **[USUARIO_REMOTO]**: Seu usuário na VM (ex: `dev_user`).
*   **[CAMINHO_PASTA_REMOTA]**: Ex: `/home/dev_user/meu-projeto`.
*   **[CAMINHO_PASTA_LOCAL]**: O local no seu Windows, entre aspas (ex: `"C:\Projetos\Backup"`).

> **Dica de Segurança:** O uso de aspas no caminho local é obrigatório se o nome de usuário do seu sistema operacional contiver espaços.

---

### 3. Conectando Cursor ou VSCode via SSH
Trabalhar diretamente nos arquivos da nuvem evita a necessidade de downloads constantes.

#### Passo A: Automatizar a configuração SSH
Execute o comando abaixo para que o gcloud crie as chaves e configure o arquivo de hosts (`~/.ssh/config`) automaticamente:
```bash
gcloud compute config-ssh
```

#### Passo B: Na IDE (Cursor ou VSCode)
1.  **Instale a Extensão:** Na galeria de extensões, instale a **"Remote - SSH"** (Microsoft).
2.  **Abrir Conexão:** 
    *   Pressione `Ctrl + Shift + P` e digite: `Remote-SSH: Connect to Host...`.
3.  **Selecione o Host:** Escolha a instância configurada no Passo A (geralmente segue o padrão `[NOME_VM].[ZONA].[PROJETO]`).
4.  **Acessar código:** Após a conexão, vá em **File > Open Folder** e informe o caminho do seu repositório na VM.

---

### Resumo de Comandos para Referência Rápida

| Objetivo | Comando |
| :--- | :--- |
| **Listar instâncias ativas** | `gcloud compute instances list` |
| **Acessar VM via terminal** | `gcloud compute ssh [NOME_VM] --zone=[ZONA]` |
| **Baixar conteúdo (Recursivo)** | `gcloud compute scp --recurse [REMOTO] [LOCAL]` |
| **Configurar túnel SSH para IDE** | `gcloud compute config-ssh` |
```

---

# 👤 Autor

Bruno Pelatieri Goulart  
Enterprise Automation Architect • AI • DevOps • n8n Specialist

---