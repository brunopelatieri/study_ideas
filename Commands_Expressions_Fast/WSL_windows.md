## ✅ **1. Comando raiz: `wsl`**

É o comando principal para controlar e gerenciar o WSL.

### ➡️ Uso básico:

| Comando         | Descrição                                                               |
| --------------- | ----------------------------------------------------------------------- |
| `wsl`           | Inicia a distribuição padrão do WSL.                                    |
| `wsl <comando>` | Executa um comando diretamente na distro padrão. Exemplo: `wsl ls -la`. |

---

## ✅ **2. Gerenciamento de distribuições**

### ➡️ Listagem:

| Comando                               | Descrição                                                       |
| ------------------------------------- | --------------------------------------------------------------- |
| `wsl --list` ou `wsl -l`              | Lista todas as distribuições instaladas.                        |
| `wsl --list --verbose` ou `wsl -l -v` | Lista as distribuições com detalhes (nome, estado, versão WSL). |
| `wsl --list --online`                 | Lista distribuições disponíveis para instalação online.         |

---

### ➡️ Seleção e configuração:

| Comando                            | Descrição                     |                                                                 |
| ---------------------------------- | ----------------------------- | --------------------------------------------------------------- |
| `wsl --set-default <NomeDistro>`   | Define a distribuição padrão. |                                                                 |
| `wsl --set-version <NomeDistro> <1 | 2>`                           | Define se a distribuição usa WSL 1 ou WSL 2.                    |
| `wsl --set-default-version <1      | 2>`                           | Define a versão padrão para novas instalações (WSL 1 ou WSL 2). |

---

## ✅ **3. Inicialização de distribuições específicas**

| Comando                | Descrição                                                                |
| ---------------------- | ------------------------------------------------------------------------ |
| `wsl -d <NomeDistro>`  | Inicia uma distribuição específica.                                      |
| `wsl -u <NomeUsuario>` | Inicia como um usuário específico. Pode ser combinado com `-d`.          |
| `wsl -e <Comando>`     | Executa um comando sem passar pelo shell padrão. Exemplo: `wsl -e bash`. |

---

## ✅ **4. Encerramento e reinicialização**

| Comando                        | Descrição                                                    |
| ------------------------------ | ------------------------------------------------------------ |
| `wsl --shutdown`               | Encerra todas as distribuições em execução e o backend WSL2. |
| `wsl --terminate <NomeDistro>` | Encerra uma distribuição específica.                         |

---

## ✅ **5. Instalação de distribuições**

| Comando                         | Descrição                              |
| ------------------------------- | -------------------------------------- |
| `wsl --install`                 | Instala o WSL e a distribuição padrão. |
| `wsl --install -d <NomeDistro>` | Instala uma distribuição específica.   |

---

## ✅ **6. Exportação e importação de distribuições**

Essencial para backup ou migração.

| Comando                                                      | Descrição                                                                    |
| ------------------------------------------------------------ | ---------------------------------------------------------------------------- |
| `wsl --export <NomeDistro> <Arquivo.tar>`                    | Exporta o sistema de arquivos da distribuição para um arquivo `.tar`.        |
| `wsl --import <NomeDistro> <DiretórioDestino> <Arquivo.tar>` | Importa uma distribuição a partir de um `.tar` para um diretório específico. |

---

## ✅ **7. Outras opções importantes**

| Comando        | Descrição                                                              |
| -------------- | ---------------------------------------------------------------------- |
| `wsl --status` | Mostra o status atual do WSL, incluindo versão padrão e configurações. |
| `wsl --help`   | Exibe ajuda e todas as opções do comando `wsl`.                        |

---

## ✅ **8. Comandos relacionados à interoperabilidade**

| Comando          | Descrição                                                                                         |
| ---------------- | ------------------------------------------------------------------------------------------------- |
| `<comando>.exe`  | Executa qualquer comando ou programa do Windows dentro do terminal Linux. Exemplo: `notepad.exe`. |
| `explorer.exe .` | Abre o diretório atual do WSL no Windows Explorer.                                                |

---

## ✅ **9. Configurações avançadas (arquivo `.wslconfig` e `wsl.conf`)**

Embora não sejam comandos diretos, é importante saber que o WSL pode ser configurado via arquivos de configuração:

* **`C:\Users\<SeuUsuário>\.wslconfig`** — configurações globais do WSL2, como alocação de memória, processadores e rede.
* **`/etc/wsl.conf`** — configurações específicas de cada distribuição, como montagem automática de discos e comportamento de rede.

---

## ✅ **Resumo visual rápido:**

```bash
wsl                             # Inicia a distro padrão
wsl -l -v                       # Lista distribuições instaladas e suas versões
wsl --set-default Ubuntu-22.04  # Define a distro padrão
wsl --set-version Ubuntu-22.04 2 # Troca para WSL 2
wsl --shutdown                  # Desliga todas as distros
wsl --export Ubuntu backup.tar  # Exporta a distro
wsl --import MyDistro D:\Linux\ MyBackup.tar # Importa a distro
wsl --install -d Debian         # Instala a distro Debian
```

---

# 👤 Autor

Bruno Pelatieri Goulart  
Enterprise Automation Architect • AI • DevOps • n8n Specialist

---

