# 📖 Guia de Operações Docker & Troubleshooting
**Foco:** Python, LangChain, LanGraph, n8n, Evolution API, Traefik e Agentes de IA.

---

### 1. Listar Containers (O dia a dia)
*   **`docker ps`**: Lista apenas os containers que estão **rodando agora**.
*   **`docker ps -a`**: Lista **todos** os containers (rodando, parados, com erro ou terminados).
*   **`docker ps -q`**: Lista apenas os **IDs** dos containers (útil para comandos em massa).
*   **`docker ps -s`**: Mostra o tamanho em disco de cada container.
*   **`docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"`**: Lista personalizada mostrando apenas Nome, Status (saúde) e Portas.

---

### 2. Listar Imagens (O que ocupa espaço)
*   **`docker images`**: Lista todas as imagens baixadas ou criadas no servidor.
*   **`docker images -a`**: Lista todas as imagens, incluindo as camadas intermediárias (layers).
*   **`docker images -f "dangling=true"`**: Lista apenas as imagens **`<none>`** (imagens órfãs que devem ser limpas).

---

### 3. Listar Redes (Conectividade)
*   **`docker network ls`**: Lista todas as redes criadas (bridge, host, n8n_default, etc).
*   **`docker network inspect <nome_da_rede>`**: Lista todos os containers que estão conectados a uma rede específica e seus respectivos IPs internos.

---

### 4. Listar Volumes (Persistência de Dados)
*   **`docker volume ls`**: Lista todos os volumes (onde ficam guardados os dados do n8n, Postgres, Redis, etc).
*   **`docker volume ls -f dangling=true`**: Lista volumes que não estão sendo usados por nenhum container (lixo).

---

### 5. Listar Consumo de Recursos (Performance)
*   **`docker stats`**: Lista em tempo real o uso de CPU, Memória e Rede de todos os containers.
*   **`docker system df`**: Lista um resumo do uso de disco por Imagens, Containers e Volumes (ótimo para saber se o disco está lotando).

---

### 6. Listar Detalhes Internos (Raio-X)
*   **`docker inspect <nome_ou_id>`**: Lista TODAS as informações de um container ou imagem (labels, redes, caminhos de pastas, variáveis de ambiente).
*   **`docker logs --tail 20 <nome>`**: Lista as últimas 20 linhas de log (o que o container está "falando").
*   **`docker port <nome>`**: Lista quais portas do container estão mapeadas para o mundo externo.

---

### 7. Comandos do Docker Compose (Por projeto)
*   **`docker compose ps`**: Lista o status apenas dos serviços definidos no arquivo da pasta atual.
*   **`docker compose images`**: Lista as imagens usadas especificamente naquele projeto.
*   **`docker compose config --services`**: Lista apenas os nomes dos serviços definidos no seu arquivo `yml`.

---

### Tabela de Atalhos Rápidos:

| O que listar? | Comando |
| :--- | :--- |
| **Containers Rodando** | `docker ps` |
| **Tudo que está parado** | `docker ps -f "status=exited"` |
| **Imagens fantasmas** | `docker images -f "dangling=true"` |
| **IPs dos containers** | `docker network inspect n8n_default` |
| **Quanto de RAM cada um usa** | `docker stats --no-stream` |

**Dica:** No Linux, você pode combinar qualquer um desses comandos com o `| grep`. 
*Exemplo para listar apenas o que é da Evolution:*
`docker ps -a | grep evolution`

---

## 🛠 1. Gerenciamento de Ciclo de Vida (Compose)
Comandos para rodar dentro das pastas onde estão os arquivos `.yml` (ex: `~/.n8n` ou `~/.evolution`).

* **Subir/Atualizar Containers (Deploy):**
    ```bash
    # O --remove-orphans limpa containers antigos que saíram do arquivo .yml
    docker compose up -d --remove-orphans
    ```
* **Baixar Imagens Novas:**
    ```bash
    docker compose pull
    ```
* **Parar e Remover Tudo do Projeto:**
    ```bash
    docker compose down
    ```
* **Reiniciar um Serviço Específico:**
    ```bash
    docker compose restart evolution-api
    ```

---

## 🔍 2. Diagnóstico e Saúde (Troubleshooting)
Essenciais para identificar por que uma URL não abre ou um container parou.

* **Verificar Status e Saúde (Health):**
    ```bash
    # Mostra se o container está 'healthy', 'starting' ou 'unhealthy'
    docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
    ```
* **Logs em Tempo Real (Onde o erro aparece):**
    ```bash
    # -f para seguir o log, --tail para ver apenas as últimas linhas
    docker logs -f --tail 100 evolution_api
    ```
* **Verificar Por Que o Healthcheck Falhou:**
    ```bash
    docker inspect --format='{{json .State.Health}}' langgraph-api
    ```
* **Consumo de Recursos (CPU/RAM):**
    ```bash
    docker stats
    ```

---

## 🌐 3. Redes e Conectividade (O "Pulo do Gato")
Comandos para resolver problemas de **502 Bad Gateway**.

* **Listar Redes Existentes:**
    ```bash
    docker network ls
    ```
* **Verificar em qual Rede o Traefik está:**
    ```bash
    docker inspect n8n-traefik-1 --format '{{json .NetworkSettings.Networks}}'
    ```
* **Testar Conexão Interna (Ignorando o Proxy):**
    ```bash
    # Testa se a API responde na porta interna do servidor
    curl -I http://localhost:8080/health
    ```

---

## 🧹 4. Limpeza e Manutenção (Housekeeping)
Importante para não deixar o disco da sua instância GCP lotar.

* **Remover Imagens "None" (Dangling Images):**
    ```bash
    # Aquelas imagens de 1.4GB que aparecem sem nome
    docker image prune -f
    ```
* **Limpeza Profunda (Cuidado):**
    ```bash
    # Remove containers parados, redes não usadas e cache de build
    docker system prune -a
    ```
* **Verificar Uso de Disco pelo Docker:**
    ```bash
    docker system df
    ```

---

## 🚨 5. Comandos de Emergência
Para quando algo trava ou entra em conflito.

* **Remover Container Conflitante:**
    ```bash
    # Use quando o nome do container já estiver em uso por um 'fantasma'
    docker rm -f nome_do_container
    ```
* **Entrar dentro do Container (Terminal Interno):**
    ```bash
    # Útil para checar arquivos internos ou conexões de rede
    docker exec -it evolution_api sh
    ```

---

## 📝 6. Padrão de Labels (Traefik)
Consulte sempre que for adicionar um novo serviço ao `docker-compose.yml`.

| Label | Função |
| :--- | :--- |
| `traefik.enable=true` | Ativa o container no Traefik |
| `traefik.http.routers.NOME.rule=Host('url.com')` | Define o domínio de acesso |
| `traefik.http.services.NOME.loadbalancer.server.port=PORTA` | Porta interna que o app usa |
| `traefik.docker.network=n8n_default` | **Obrigatória** se o container estiver em >1 rede |

---

### 💡 Dicas de Especialista para seu Ambiente:
1.  **Ordem de Reinicialização:** Se o Traefik não reconhecer um novo domínio, reinicie ele por último: `docker restart n8n-traefik-1`.
2.  **Segurança:** Evite usar a seção `ports:` (ex: `8080:8080`) para serviços que já passam pelo Traefik. Isso mantém as portas fechadas para o mundo externo, permitindo acesso apenas via HTTPS.
3.  **Logs do Traefik:** Se o SSL não gerar, olhe os logs do Traefik; geralmente é erro de digitação no e-mail ou limite da Let's Encrypt.

---

# 👤 Autor

Bruno Pelatieri Goulart  
Enterprise Automation Architect • AI • DevOps • n8n Specialist

---

© 2026 – Documentação técnica unificada.
