# 🚀 Guia de Implantação: Evolution Go (Versão Premium)

Este documento descreve o processo de deploy da **Evolution API (Engine Go)** em ambiente **Docker Swarm**, utilizando **Traefik** como Proxy Reverso e **PostgreSQL** como banco de dados persistente.

## 🏗️ 1. Docker Compose / Stack (YAML)

Copie o código abaixo para o seu editor de Stacks no Portainer. Substitua os valores entre `< >` pelas suas informações reais.

```yaml
version: "3.8"

services:
  evolution_go:
    image: evoapicloud/evolution-go:latest
    networks:
      - bru # Substitua pelo nome da sua rede externa
    environment:
      # --- 🖥️ CONFIGURAÇÕES DE SERVIDOR ---
      - SERVER_PORT=8080
      - CLIENT_NAME=evolution # Nome identificador do seu cliente/projeto
      - GLOBAL_API_KEY=<SUA_CHAVE_MESTRA_AQUI> # Chave de segurança para acessar a API
      
      # --- 🗄️ BANCO DE DADOS (POSTGRESQL) ---
      # O motor Go exige a definição separada para os contextos de Autenticação e Usuários.
      - POSTGRES_AUTH_DB=postgresql://<USUARIO>:<SENHA>@<HOST_POSTGRES>:5432/<BANCO_EVOLUTION>?sslmode=disable
      - POSTGRES_USERS_DB=postgresql://<USUARIO>:<SENHA>@<HOST_POSTGRES>:5432/<BANCO_EVOLUTION>?sslmode=disable
      
      # --- 📊 LOGS E MONITORAMENTO ---
      - WADEBUG=INFO # Níveis: DEBUG, INFO, WARN, ERROR
      - LOGTYPE=console # Opções: console, json
      
      # --- ⚙️ OPERAÇÃO E PERSISTÊNCIA ---
      - DATABASE_SAVE_MESSAGES=true # Habilita salvar mensagens no banco
      - CONNECT_ON_STARTUP=true # Reconecta instâncias automaticamente no boot
      - WEBHOOKFILES=true # Inclui arquivos/base64 nos disparos de Webhook
      
      # --- 🐇 MENSAGERIA E STORAGE (OPCIONAIS) ---
      - AMQP_GLOBAL_ENABLED=false
      - MINIO_ENABLED=false

    deploy:
      mode: replicated
      replicas: 1
      placement:
        constraints:
          - node.role == manager
      labels:
        # Roteamento Inteligente Traefik
        - traefik.enable=1
        - traefik.http.routers.evogo_router.rule=Host(`<SEU_DOMINIO_URL>`)
        - traefik.http.routers.evogo_router.entrypoints=websecure
        - traefik.http.routers.evogo_router.priority=1
        - traefik.http.routers.evogo_router.tls.certresolver=letsencryptresolver
        - traefik.http.routers.evogo_router.service=evogo_service
        - traefik.http.services.evogo_service.loadbalancer.server.port=8080
        - traefik.http.services.evogo_service.loadbalancer.passHostHeader=true

networks:
  bru:
    external: true
```

-----

## 🧹 2. Preparação do Ambiente (Obrigatório)

Diferente da versão em Node.js, a **Evolution Go** utiliza uma estrutura de banco de dados otimizada. **Não tente reaproveitar o banco de dados da versão anterior sem limpá-lo.**

1.  **Limpeza do Banco:** Acesse seu PostgreSQL e certifique-se de que o banco `evolution` está vazio ou recém-criado.
2.  **Rede Docker:** Verifique se a rede definida no YAML (`bru`) já existe no Swarm:
      * `docker network ls`
3.  **Certificados:** Garanta que seu Traefik está configurado com o `certresolver` correto (ex: `letsencryptresolver`) para gerar o SSL automaticamente.

-----

## ⚙️ 3. Dicionário de Variáveis Críticas

| Variável | Função | Observação |
| :--- | :--- | :--- |
| `GLOBAL_API_KEY` | Autenticação Global | Substitui a antiga `AUTHENTICATION_API_KEY`. |
| `POSTGRES_AUTH_DB` | Banco de Credenciais | Utilizado para gerenciar tokens e sessões das instâncias. |
| `POSTGRES_USERS_DB` | Banco de Dados de Uso | Utilizado para logs de mensagens e dados de contatos. |
| `passHostHeader` | Preservação de Host | Essencial para que a API identifique corretamente sua própria URL. |

-----

## 🛠️ 4. Passo a Passo do Deploy

1.  **Portainer:** Vá em **Stacks** -\> **Add Stack**.
2.  **Configuração:** Dê o nome de `evolution-go` e cole o YAML ajustado com suas credenciais.
3.  **Deploy:** Clique em **Deploy the stack**.
4.  **Monitoramento:** Abra os Logs do container. A primeira inicialização executará as `Database Migrations` automaticamente.
5.  **Teste de Vida:** Acesse `https://<SEU_DOMINIO_URL>/health`. Se retornar `200 OK`, a API está pronta para operar.

-----

## 🔗 5. Documentação e Apoio

  * **Documentação Oficial Evolution Go:** [Evolution Foundation Docs](https://www.google.com/search?q=https://docs.evolutionfoundation.com.br/evolution-go/introduction)
  * **Docker Hub (Imagens Oficiais):** [evoapicloud/evolution-go](https://www.google.com/search?q=https://hub.docker.com/r/evoapicloud/evolution-go)
  * **Comunidade de Suporte:** [Discord Oficial da Evolution](https://www.google.com/search?q=https://discord.gg/evolution-api)

-----

> **Dica de Sênior:** Nesta build específica de Março de 2026, a dependência do **Redis** foi removida para simplificar a infraestrutura em implementações de médio porte. Se você planeja escalar para milhares de instâncias, considere ativar o **RabbitMQ (AMQP)** para gerenciar as filas de mensagens de forma assíncrona.

**Tudo pronto\! Sua infraestrutura está limpa, segura e documentada.**

---

# 👤 Autor

Bruno Pelatieri Goulart  
Enterprise AI Workflow Architect  
LLM Orchestration • Deterministic Automation • n8n Systems

---

© 2026 – Documentação técnica unificada.