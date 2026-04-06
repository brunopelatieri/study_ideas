# 📦 Stack de Instalação — Evolution API version latest (2.3.8) - Redis

> Documentação de referência para instalação e configuração da stack completa.  
> ⚠️ **Substitua todos os campos marcados com `<coloque suas credenciais aqui>` antes de subir os serviços.**

---

## 🗂️ Índice

- [📦 Stack de Instalação — Evolution API version latest (2.3.8) - Redis](#-stack-de-instalação--evolution-api-version-latest-238---redis)
  - [🗂️ Índice](#️-índice)
  - [🌐 Redes Docker](#-redes-docker)
  - [🔴 Redis (Acesso Externo)](#-redis-acesso-externo)
    - [Subindo o Redis](#subindo-o-redis)
    - [Testando a conexão](#testando-a-conexão)
  - [🤖 Evolution API](#-evolution-api)
    - [Subindo a Evolution API](#subindo-a-evolution-api)
  - [🔗 Strings de Conexão](#-strings-de-conexão)
  - [🛠️ Comandos Úteis](#️-comandos-úteis)
  - [✅ Checklist de Segurança](#-checklist-de-segurança)
    - [Protegendo a porta do Redis com UFW](#protegendo-a-porta-do-redis-com-ufw)
- [👤 Autor](#-autor)

---

## 🌐 Redes Docker

| Network ID | Nome | Driver | Scope |
|---|---|---|---|
| `192c449d2b3f` | bridge | bridge | local |
| `d927284083ab` | evolution_default | bridge | local |
| `5aeb7bc23a60` | host | host | local |
| `f307431fd947` | n8n-compose_default | bridge | local |
| `63a19374cffd` | n8n_default | bridge | local |
| `2066125b8436` | none | null | local |

> A Evolution API e o Redis devem estar conectados às redes `n8n_default` e `evolution_default` para comunicação interna.

---

## 🔴 Redis (Acesso Externo)

**Arquivo:** `docker-compose.redis.yml`

```yaml
version: '3.8'

services:
  redis:
    image: redis:7-alpine
    container_name: redis-external
    restart: always
    command: >
      redis-server
      --appendonly yes
      --requirepass "<coloque suas credenciais aqui>"
      --bind 0.0.0.0
      --protected-mode no
      --maxmemory 512mb
      --maxmemory-policy allkeys-lru
    ports:
      - "6379:6379"
    volumes:
      - redis_external_data:/data
    environment:
      - REDIS_PASSWORD=<coloque suas credenciais aqui>
    healthcheck:
      test: ["CMD", "redis-cli", "-a", "<coloque suas credenciais aqui>", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - n8n_default
      - evolution_default

volumes:
  redis_external_data:
    driver: local

networks:
  n8n_default:
    external: true
    name: n8n_default
  evolution_default:
    external: true
    name: evolution_default
```

### Subindo o Redis

```bash
docker compose -f docker-compose.redis.yml up -d
```

### Testando a conexão

```bash
# Local
redis-cli -h localhost -p 6379 -a "<coloque suas credenciais aqui>" ping

# Remoto
redis-cli -h <IP_DO_SERVIDOR> -p 6379 -a "<coloque suas credenciais aqui>" ping
```

---

## 🤖 Evolution API

**Arquivo:** `docker-compose.yml`

```yaml
version: '3.8'

services:
  evolution-api:
    image: evoapicloud/evolution-api:latest
    restart: always
    depends_on:
      - evolution-redis
      - evolution-postgres
    environment:

    ## ⚙️ Configurações Gerais
      - SERVER_URL=https://sua-url
      - AUTHENTICATION_API_KEY=<coloque suas credenciais aqui>
      - AUTHENTICATION_EXPOSE_IN_FETCH_INSTANCES=true
      - DEL_INSTANCE=false
      - QRCODE_LIMIT=1902
      - LANGUAGE=pt-BR
      - CONFIG_SESSION_PHONE_CLIENT=Escolha-Um-Nome
      - CONFIG_SESSION_PHONE_NAME=Chrome

    ## 🗄️ Banco de Dados
      - DATABASE_ENABLED=true
      - DATABASE_PROVIDER=postgresql
      - DATABASE_CONNECTION_URI=postgresql://evo:<coloque suas credenciais aqui>@evolution-postgres:5432/evolution?schema=public
      - DATABASE_CONNECTION_CLIENT_NAME=evolution_api
      - DATABASE_SAVE_DATA_INSTANCE=true
      - DATABASE_SAVE_DATA_NEW_MESSAGE=true
      - DATABASE_SAVE_MESSAGE_UPDATE=true
      - DATABASE_SAVE_DATA_CONTACTS=true
      - DATABASE_SAVE_DATA_CHATS=true
      - DATABASE_SAVE_DATA_LABELS=true
      - DATABASE_SAVE_DATA_HISTORIC=true

    ## 🤖 Integrações
      - N8N_ENABLED=true
      - EVOAI_ENABLED=true
      - OPENAI_ENABLED=true
      - DIFY_ENABLED=true
      - TYPEBOT_ENABLED=true
      - TYPEBOT_API_VERSION=latest
      - CHATWOOT_ENABLED=false

    ## 🧊 Cache Redis
      - CACHE_REDIS_ENABLED=true
      - CACHE_REDIS_URI=redis://evolution-redis:6379/6
      - CACHE_REDIS_PREFIX_KEY=evolution
      - CACHE_REDIS_SAVE_INSTANCES=false
      - CACHE_LOCAL_ENABLED=false

    ## ☁️ S3 (desativado)
      - S3_ENABLED=false

    ## 💼 WhatsApp Business
      - WA_BUSINESS_TOKEN_WEBHOOK=<coloque suas credenciais aqui>
      - WA_BUSINESS_URL=https://graph.facebook.com
      - WA_BUSINESS_VERSION=v24.0
      - WA_BUSINESS_LANGUAGE=pt_BR

    ## 🌐 Webhook Global (desativado)
      - WEBHOOK_GLOBAL_ENABLED=false

    ## 📊 Telemetria (desativada)
      - TELEMETRY=false

    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.evolution.rule=Host(`sua-url`)"
      - "traefik.http.routers.evolution.entrypoints=websecure"
      - "traefik.http.routers.evolution.tls.certresolver=myresolver"
    networks:
      - default
      - traefik_net

  evolution-redis:
    image: redis:alpine
    restart: always
    command: redis-server --appendonly yes
    volumes:
      - redis_data:/data

  evolution-postgres:
    image: postgres:15-alpine
    restart: always
    environment:
      - POSTGRES_USER=evo
      - POSTGRES_PASSWORD=<coloque suas credenciais aqui>
      - POSTGRES_DB=evolution
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
  redis_data:

networks:
  traefik_net:
    external: true
    name: n8n_default
```

### Subindo a Evolution API

```bash
docker compose up -d
```

---

## 🔗 Strings de Conexão

| Serviço | String |
|---|---|
| **Redis (interno)** | `redis://evolution-redis:6379/6` |
| **Redis (externo com auth)** | `redis://:<coloque suas credenciais aqui>@<IP_DO_SERVIDOR>:6379/0` |
| **PostgreSQL (Evolution)** | `postgresql://evo:<coloque suas credenciais aqui>@evolution-postgres:5432/evolution?schema=public` |
| **PostgreSQL (Chatwoot)** | `postgresql://postgres:<coloque suas credenciais aqui>@pgvector:5432/chatwoot?sslmode=disable` |

---

## 🛠️ Comandos Úteis

```bash
# Ver todos os containers rodando
docker ps

# Ver logs da Evolution API
docker logs -f evolution-api

# Ver logs do Redis
docker logs -f redis-external

# Reiniciar um serviço específico
docker compose restart evolution-api

# Verificar redes Docker
docker network ls

# Inspecionar uma rede
docker network inspect n8n_default

# Acessar o Redis CLI interativo
docker exec -it redis-external redis-cli -a "<coloque suas credenciais aqui>"

# Acessar o PostgreSQL
docker exec -it evolution-postgres psql -U evo -d evolution
```

---

## ✅ Checklist de Segurança

- [ ] Substituir **todas** as senhas e chaves marcadas com `<coloque suas credenciais aqui>`
- [ ] Restringir a porta `6379` no firewall apenas para IPs confiáveis
- [ ] Configurar `ufw` ou regras equivalentes no provedor de cloud
- [ ] Não versionar os arquivos `.yml` com credenciais em repositórios públicos
- [ ] Usar um arquivo `.env` para gerenciar variáveis sensíveis
- [ ] Verificar se o Traefik está configurado com TLS para o subdomínio `sua-url`
- [ ] Confirmar que `TELEMETRY=false` está ativo se não quiser enviar dados externos

### Protegendo a porta do Redis com UFW

```bash
# Bloquear acesso geral à porta 6379
ufw deny 6379

# Liberar apenas para IP confiável
ufw allow from <SEU_IP_CONFIAVEL> to any port 6379

# Verificar regras
ufw status verbose
```

---

# 👤 Autor

Bruno Pelatieri Goulart  
Enterprise AI Workflow Architect  
LLM Orchestration • Deterministic Automation • n8n Systems

---

© 2026 – Documentação técnica unificada.