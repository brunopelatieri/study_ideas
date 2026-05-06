# ☁️ Guia Completo de Comandos gcloud CLI — Google Cloud Platform do Zero ao Deploy em Produção

> Referência prática e completa de comandos `gcloud` para desenvolvedores iniciando no GCP: autenticação, projetos, Cloud Run, Cloud Functions, Cloud Storage, Cloud SQL e Secrets Manager — com exemplos reais e explicações linha a linha.

---

[![GCP](https://img.shields.io/badge/Google%20Cloud-Platform-4285F4?style=flat-square&logo=googlecloud&logoColor=white)](https://cloud.google.com)
[![gcloud CLI](https://img.shields.io/badge/gcloud%20CLI-v500%2B-green?style=flat-square)](https://cloud.google.com/sdk/docs)
[![Last Updated](https://img.shields.io/badge/Atualizado-Maio%202026-blue?style=flat-square)](.)
[![License](https://img.shields.io/badge/Licen%C3%A7a-MIT-lightgrey?style=flat-square)](.)

---

## 📋 Sumário

- [Instalação e Configuração Inicial](#-instalação-e-configuração-inicial)
- [Autenticação — auth](#-autenticação--auth)
- [Projetos e Configuração — config](#-projetos-e-configuração--config)
- [Cloud Run](#-cloud-run)
- [Cloud Functions](#-cloud-functions)
- [Cloud Storage — gsutil e gcloud storage](#-cloud-storage--gsutil-e-gcloud-storage)
- [Cloud SQL](#-cloud-sql)
- [Secrets Manager](#-secrets-manager)
- [IAM — Permissões Essenciais](#-iam--permissões-essenciais)
- [Logs e Monitoramento](#-logs-e-monitoramento)
- [Cheat Sheet — Referência Rápida](#-cheat-sheet--referência-rápida)
- [Erros Comuns e Soluções](#-erros-comuns-e-soluções)

---

## 🚀 Instalação e Configuração Inicial

### Instalar o Google Cloud SDK

```bash
# macOS (via Homebrew)
brew install --cask google-cloud-sdk

# Linux (Debian/Ubuntu)
curl https://sdk.cloud.google.com | bash
exec -l $SHELL

# Windows — baixe o instalador em:
# https://cloud.google.com/sdk/docs/install

# Verificar versão instalada
gcloud version

# Atualizar para a versão mais recente
gcloud components update
```

### Instalar componentes adicionais

```bash
# Kubectl (para Kubernetes)
gcloud components install kubectl

# Beta commands (acesso a features em preview)
gcloud components install beta

# Alpha commands
gcloud components install alpha

# Ver todos os componentes disponíveis
gcloud components list
```

---

## 🔐 Autenticação — auth

> **O que é:** antes de usar qualquer serviço do GCP via terminal, você precisa provar quem é. Existem dois tipos de autenticação: com sua conta pessoal (para desenvolvimento) e com Service Account (para servidores/CI-CD).

### Login com conta Google (desenvolvimento local)

```bash
# Login interativo — abre o navegador
gcloud auth login

# Login sem abrir navegador (para ambientes remotos/SSH)
gcloud auth login --no-launch-browser

# Ver qual conta está autenticada
gcloud auth list

# Definir conta ativa (se tiver mais de uma)
gcloud config set account SEU_EMAIL@gmail.com

# Revogar autenticação da conta atual
gcloud auth revoke

# Revogar todas as contas
gcloud auth revoke --all
```

### Application Default Credentials (ADC)

> **O que é ADC:** credenciais que os SDKs (Python, Node.js etc.) usam automaticamente para se autenticar com a API do GCP no seu código local.

```bash
# Configurar ADC para desenvolvimento local
# (roda uma vez, os SDKs vão usar automaticamente)
gcloud auth application-default login

# Ver onde as credenciais ADC estão salvas
gcloud auth application-default print-access-token

# Revogar ADC
gcloud auth application-default revoke
```

### Service Account (produção / CI-CD)

> **O que é:** uma conta de serviço é uma "identidade de máquina" — usada por servidores, containers e pipelines para se autenticar sem interação humana.

```bash
# Criar uma service account
gcloud iam service-accounts create minha-sa \
  --display-name="Minha Service Account" \
  --description="SA para minha aplicação"

# Listar service accounts do projeto
gcloud iam service-accounts list

# Gerar chave JSON para a SA (para usar localmente ou em CI)
gcloud iam service-accounts keys create chave.json \
  --iam-account=minha-sa@MEU_PROJETO.iam.gserviceaccount.com

# Autenticar com a chave JSON
gcloud auth activate-service-account \
  --key-file=chave.json

# ⚠️ NUNCA commite o arquivo chave.json no Git!
# Adicione ao .gitignore:
echo "chave.json" >> .gitignore
```

---

## ⚙️ Projetos e Configuração — config

> **O que é:** no GCP, tudo vive dentro de um projeto. O gcloud precisa saber em qual projeto e em qual região você quer trabalhar. A `config` guarda essas preferências.

### Gerenciar projetos

```bash
# Listar todos os seus projetos
gcloud projects list

# Criar um novo projeto
gcloud projects create meu-projeto-novo \
  --name="Meu Projeto" \
  --set-as-default

# Ver detalhes de um projeto
gcloud projects describe meu-projeto-id

# Deletar projeto (⚠️ irreversível após 30 dias)
gcloud projects delete meu-projeto-id
```

### Configurar o ambiente (config)

```bash
# Ver a configuração atual completa
gcloud config list

# Definir o projeto padrão
gcloud config set project MEU_PROJETO_ID

# Definir a região padrão (onde seus recursos serão criados)
# Regiões no Brasil: southamerica-east1 (São Paulo)
gcloud config set compute/region southamerica-east1

# Definir a zona padrão
gcloud config set compute/zone southamerica-east1-a

# Ver o projeto atualmente configurado
gcloud config get-value project

# Ver a região atualmente configurada
gcloud config get-value compute/region
```

### Múltiplos ambientes com named configurations

> **Dica:** se você trabalha com vários projetos (dev, staging, prod), crie configurações nomeadas para trocar rapidamente.

```bash
# Criar uma nova configuração nomeada
gcloud config configurations create dev
gcloud config set project meu-projeto-dev
gcloud config set compute/region southamerica-east1
gcloud config set account dev@empresa.com

# Criar configuração de produção
gcloud config configurations create prod
gcloud config set project meu-projeto-prod
gcloud config set account prod@empresa.com

# Listar configurações disponíveis
gcloud config configurations list

# Trocar para uma configuração
gcloud config configurations activate prod

# Ver qual configuração está ativa
gcloud config configurations list --filter="is_active=true"
```

### Habilitar APIs necessárias

> **Importante:** no GCP, cada serviço (Cloud Run, Cloud SQL etc.) precisa ter sua API habilitada no projeto antes de usar.

```bash
# Habilitar uma API
gcloud services enable run.googleapis.com

# Habilitar múltiplas APIs de uma vez
gcloud services enable \
  run.googleapis.com \
  cloudfunctions.googleapis.com \
  sqladmin.googleapis.com \
  storage.googleapis.com \
  secretmanager.googleapis.com \
  cloudbuild.googleapis.com

# Listar APIs habilitadas no projeto
gcloud services list --enabled

# Listar APIs disponíveis (pode ser longa)
gcloud services list --available

# Desabilitar uma API (⚠️ pode quebrar serviços dependentes)
gcloud services disable run.googleapis.com
```

---

## 🏃 Cloud Run

> **O que é:** serviço serverless para rodar containers. Você sobe sua imagem Docker e o GCP cuida de escalonamento, HTTPS, domínio etc. Paga apenas pelo que usar. Ideal para APIs, webhooks e microsserviços.

### Deploy de container

```bash
# Deploy de uma imagem já no Container Registry / Artifact Registry
gcloud run deploy meu-servico \
  --image=gcr.io/MEU_PROJETO/minha-imagem:latest \
  --region=southamerica-east1 \
  --platform=managed

# Deploy com todas as opções comuns
gcloud run deploy minha-api \
  --image=gcr.io/MEU_PROJETO/minha-api:v1.0 \
  --region=southamerica-east1 \
  --platform=managed \
  --allow-unauthenticated \          # torna público (sem auth)
  --port=8080 \                      # porta que seu app escuta
  --memory=512Mi \                   # memória alocada
  --cpu=1 \                          # vCPUs
  --min-instances=0 \                # escala até zero (economiza custo)
  --max-instances=10 \               # limite de instâncias
  --timeout=300 \                    # timeout em segundos
  --concurrency=80                   # requisições simultâneas por instância

# Deploy com variáveis de ambiente
gcloud run deploy minha-api \
  --image=gcr.io/MEU_PROJETO/minha-api:latest \
  --region=southamerica-east1 \
  --set-env-vars="NODE_ENV=production,PORT=8080,DB_HOST=localhost"

# Deploy usando variáveis de ambiente de um arquivo .env
# (crie um arquivo env.yaml com chave: valor)
gcloud run deploy minha-api \
  --image=gcr.io/MEU_PROJETO/minha-api:latest \
  --region=southamerica-east1 \
  --env-vars-file=env.yaml
```

**Exemplo de `env.yaml`:**
```yaml
NODE_ENV: production
PORT: "8080"
DATABASE_URL: postgres://user:pass@host/db
```

### Build + Deploy direto do código fonte

```bash
# Build automático via Cloud Build + deploy (sem Dockerfile local)
# O GCP detecta a linguagem e faz o build
gcloud run deploy minha-api \
  --source=. \
  --region=southamerica-east1 \
  --allow-unauthenticated
```

### Gerenciar serviços

```bash
# Listar todos os serviços Cloud Run do projeto
gcloud run services list --region=southamerica-east1

# Ver detalhes de um serviço (URL, configuração, revisões)
gcloud run services describe meu-servico \
  --region=southamerica-east1

# Obter apenas a URL do serviço
gcloud run services describe meu-servico \
  --region=southamerica-east1 \
  --format="value(status.url)"

# Deletar um serviço
gcloud run services delete meu-servico \
  --region=southamerica-east1

# Atualizar variáveis de ambiente sem novo deploy
gcloud run services update meu-servico \
  --region=southamerica-east1 \
  --set-env-vars="DEBUG=true"

# Remover uma variável de ambiente
gcloud run services update meu-servico \
  --region=southamerica-east1 \
  --remove-env-vars="DEBUG"
```

### Revisões e tráfego

> **O que é revisão:** cada deploy cria uma nova revisão. Você pode dividir tráfego entre revisões (útil para canary deploys).

```bash
# Listar revisões de um serviço
gcloud run revisions list \
  --service=meu-servico \
  --region=southamerica-east1

# Dividir tráfego entre revisões (canary deploy)
# 90% para revisão estável, 10% para nova
gcloud run services update-traffic meu-servico \
  --region=southamerica-east1 \
  --to-revisions=meu-servico-00005-abc=90,meu-servico-00006-xyz=10

# Enviar 100% do tráfego para a revisão mais recente
gcloud run services update-traffic meu-servico \
  --region=southamerica-east1 \
  --to-latest

# Deletar uma revisão específica
gcloud run revisions delete meu-servico-00003-abc \
  --region=southamerica-east1
```

### Logs do Cloud Run

```bash
# Ver logs em tempo real (streaming)
gcloud run services logs tail meu-servico \
  --region=southamerica-east1

# Ver últimas 50 linhas de log
gcloud logging read \
  "resource.type=cloud_run_revision AND resource.labels.service_name=meu-servico" \
  --limit=50 \
  --format="table(timestamp,textPayload)"

# Filtrar apenas erros
gcloud logging read \
  "resource.type=cloud_run_revision \
   AND resource.labels.service_name=meu-servico \
   AND severity>=ERROR" \
  --limit=20
```

### Conectar Cloud Run ao Secrets Manager

```bash
# Montar um secret como variável de ambiente no serviço
gcloud run services update meu-servico \
  --region=southamerica-east1 \
  --set-secrets="DB_PASSWORD=minha-senha-db:latest"

# Montar como arquivo (útil para certificados, chaves)
gcloud run services update meu-servico \
  --region=southamerica-east1 \
  --set-secrets="/secrets/config=meu-config:latest"
```

---

## ⚡ Cloud Functions

> **O que é:** execute código em resposta a eventos (HTTP, Pub/Sub, Cloud Storage, Firestore etc.) sem gerenciar servidores. Ideal para webhooks, triggers e processamento de eventos.

### Deploy de funções (Gen 2 — recomendado)

> **Gen 1 vs Gen 2:** a segunda geração usa Cloud Run por baixo, tem melhor performance, mais memória/CPU disponível e suporte a tráfego paralelo. Use Gen 2 em projetos novos.

```bash
# Deploy de função HTTP simples (Gen 2)
gcloud functions deploy minha-funcao \
  --gen2 \
  --runtime=nodejs20 \
  --region=southamerica-east1 \
  --source=. \
  --entry-point=handler \           # nome da função exportada no código
  --trigger-http \
  --allow-unauthenticated

# Deploy com Python
gcloud functions deploy processar-dados \
  --gen2 \
  --runtime=python312 \
  --region=southamerica-east1 \
  --source=. \
  --entry-point=main \
  --trigger-http \
  --allow-unauthenticated \
  --memory=256Mi \
  --timeout=60s

# Deploy com variáveis de ambiente
gcloud functions deploy minha-funcao \
  --gen2 \
  --runtime=nodejs20 \
  --region=southamerica-east1 \
  --source=. \
  --entry-point=handler \
  --trigger-http \
  --set-env-vars="API_KEY=abc123,ENV=production"

# Deploy com mais recursos
gcloud functions deploy funcao-pesada \
  --gen2 \
  --runtime=nodejs20 \
  --region=southamerica-east1 \
  --source=. \
  --entry-point=processar \
  --trigger-http \
  --memory=1Gi \
  --cpu=2 \
  --timeout=300s \
  --max-instances=5
```

### Triggers além de HTTP

```bash
# Trigger por mensagem no Pub/Sub
gcloud functions deploy processar-evento \
  --gen2 \
  --runtime=nodejs20 \
  --region=southamerica-east1 \
  --source=. \
  --entry-point=onMessage \
  --trigger-topic=meu-topico

# Trigger por arquivo no Cloud Storage (quando arquivo é criado)
gcloud functions deploy processar-upload \
  --gen2 \
  --runtime=python312 \
  --region=southamerica-east1 \
  --source=. \
  --entry-point=on_file_upload \
  --trigger-event-filters="type=google.cloud.storage.object.v1.finalized" \
  --trigger-event-filters="bucket=meu-bucket"
```

### Gerenciar funções

```bash
# Listar todas as funções
gcloud functions list --region=southamerica-east1

# Ver detalhes de uma função (URL, trigger, configuração)
gcloud functions describe minha-funcao \
  --region=southamerica-east1

# Obter a URL de uma função HTTP
gcloud functions describe minha-funcao \
  --region=southamerica-east1 \
  --format="value(serviceConfig.uri)"

# Deletar uma função
gcloud functions delete minha-funcao \
  --region=southamerica-east1

# Chamar uma função HTTP para testar
gcloud functions call minha-funcao \
  --region=southamerica-east1 \
  --data='{"nome": "João"}'
```

### Logs de funções

```bash
# Ver logs em tempo real
gcloud functions logs read minha-funcao \
  --region=southamerica-east1 \
  --limit=50

# Streaming de logs
gcloud alpha functions logs tail minha-funcao \
  --region=southamerica-east1
```

### Runtimes disponíveis

```bash
# Ver todos os runtimes suportados
gcloud functions runtimes list --region=southamerica-east1

# Exemplos de runtime:
# nodejs20, nodejs22
# python311, python312
# go122, go123
# java21
# ruby33
# php83
```

---

## 🪣 Cloud Storage — gsutil e gcloud storage

> **O que é:** armazenamento de objetos (arquivos) na nuvem. Pense no S3 da AWS. Usado para backups, assets estáticos, datasets, logs, imagens etc. Um "bucket" é como uma pasta raiz.

> **gsutil vs gcloud storage:** `gsutil` é o comando legado (ainda funciona). `gcloud storage` é o novo, mais rápido e com melhor paralelismo. Prefira `gcloud storage` em projetos novos.

### Criar e gerenciar buckets

```bash
# Criar um bucket (nome deve ser globalmente único no GCP)
gcloud storage buckets create gs://meu-bucket-unico \
  --location=southamerica-east1 \
  --default-storage-class=STANDARD

# Classes de armazenamento:
# STANDARD   → acesso frequente, maior custo
# NEARLINE   → acesso ~1x/mês
# COLDLINE   → acesso ~1x/trimestre
# ARCHIVE    → acesso raramente, menor custo

# Criar bucket com versioning habilitado
gcloud storage buckets create gs://meu-bucket \
  --location=southamerica-east1 \
  --enable-versioning

# Listar seus buckets
gcloud storage buckets list

# Ver detalhes de um bucket
gcloud storage buckets describe gs://meu-bucket

# Deletar bucket (⚠️ precisa estar vazio ou usar --force)
gcloud storage buckets delete gs://meu-bucket
```

### Upload e download de arquivos

```bash
# Upload de um arquivo
gcloud storage cp arquivo.txt gs://meu-bucket/

# Upload para uma "pasta" (prefixo)
gcloud storage cp arquivo.txt gs://meu-bucket/pasta/arquivo.txt

# Upload de uma pasta inteira (recursivo)
gcloud storage cp -r ./minha-pasta gs://meu-bucket/destino/

# Download de um arquivo
gcloud storage cp gs://meu-bucket/arquivo.txt ./local/

# Download de uma pasta inteira
gcloud storage cp -r gs://meu-bucket/pasta/ ./local/

# Upload paralelo de muitos arquivos (mais rápido)
gcloud storage cp -r ./dados/ gs://meu-bucket/ \
  --parallel-composite-upload-threshold=150M
```

### Listar e gerenciar objetos

```bash
# Listar objetos de um bucket
gcloud storage ls gs://meu-bucket/

# Listar recursivamente
gcloud storage ls -r gs://meu-bucket/

# Listar com detalhes (tamanho, data)
gcloud storage ls -l gs://meu-bucket/

# Ver tamanho total de um bucket
gcloud storage du gs://meu-bucket/ --summarize

# Mover/renomear objeto
gcloud storage mv gs://meu-bucket/antigo.txt gs://meu-bucket/novo.txt

# Copiar entre buckets
gcloud storage cp gs://bucket-origem/arquivo.txt gs://bucket-destino/

# Deletar um objeto
gcloud storage rm gs://meu-bucket/arquivo.txt

# Deletar pasta inteira (recursivo)
gcloud storage rm -r gs://meu-bucket/pasta/

# Sincronizar pasta local com bucket (como rsync)
gcloud storage rsync ./local/ gs://meu-bucket/destino/ --recursive
```

### Permissões e acesso público

```bash
# Tornar um objeto público (leitura por qualquer um)
gcloud storage objects update gs://meu-bucket/imagem.png \
  --add-acl-grant=entity=allUsers,role=READER

# Tornar TODOS os objetos do bucket públicos
gcloud storage buckets update gs://meu-bucket \
  --predefined-default-object-acl=publicRead

# Gerar URL assinada (acesso temporário sem tornar público)
# Válida por 1 hora
gcloud storage sign-url gs://meu-bucket/arquivo-privado.pdf \
  --duration=1h \
  --private-key-file=chave.json

# Remover acesso público
gcloud storage buckets update gs://meu-bucket \
  --no-public-access-prevention=false
```

### Ciclo de vida dos objetos (lifecycle)

```bash
# Aplicar regra de lifecycle via arquivo JSON
# (ex: deletar objetos com mais de 30 dias)
gcloud storage buckets update gs://meu-bucket \
  --lifecycle-file=lifecycle.json
```

**Exemplo de `lifecycle.json`:**
```json
{
  "lifecycle": {
    "rule": [
      {
        "action": {"type": "Delete"},
        "condition": {"age": 30}
      },
      {
        "action": {
          "type": "SetStorageClass",
          "storageClass": "COLDLINE"
        },
        "condition": {"age": 90}
      }
    ]
  }
}
```

---

## 🗄️ Cloud SQL

> **O que é:** banco de dados relacional gerenciado no GCP. Suporta PostgreSQL, MySQL e SQL Server. O GCP cuida de backups, patches, alta disponibilidade e failover.

### Criar instância

```bash
# Criar instância PostgreSQL
gcloud sql instances create minha-instancia \
  --database-version=POSTGRES_15 \
  --tier=db-f1-micro \               # menor tier (dev/teste)
  --region=southamerica-east1 \
  --storage-type=SSD \
  --storage-size=10GB

# Tiers comuns:
# db-f1-micro    → 1 vCPU, 614MB RAM  (dev)
# db-g1-small    → 1 vCPU, 1.7GB RAM  (pequeno)
# db-n1-standard-2 → 2 vCPU, 7.5GB RAM (produção)

# Criar instância MySQL
gcloud sql instances create minha-instancia-mysql \
  --database-version=MYSQL_8_0 \
  --tier=db-n1-standard-1 \
  --region=southamerica-east1

# Criar instância com alta disponibilidade (produção)
gcloud sql instances create prod-db \
  --database-version=POSTGRES_15 \
  --tier=db-n1-standard-2 \
  --region=southamerica-east1 \
  --availability-type=REGIONAL \    # HA com failover automático
  --backup-start-time=02:00 \
  --retained-backups-count=7

# Listar instâncias
gcloud sql instances list

# Ver detalhes de uma instância
gcloud sql instances describe minha-instancia
```

### Usuários e senhas

```bash
# Definir senha do usuário postgres (root)
gcloud sql users set-password postgres \
  --instance=minha-instancia \
  --password=SENHA_FORTE_AQUI

# Criar novo usuário
gcloud sql users create meu-usuario \
  --instance=minha-instancia \
  --password=SENHA_DO_USUARIO

# Listar usuários
gcloud sql users list --instance=minha-instancia

# Deletar usuário
gcloud sql users delete meu-usuario \
  --instance=minha-instancia
```

### Bancos de dados

```bash
# Criar um banco de dados na instância
gcloud sql databases create meu-banco \
  --instance=minha-instancia

# Listar bancos de dados
gcloud sql databases list --instance=minha-instancia

# Deletar banco de dados
gcloud sql databases delete meu-banco \
  --instance=minha-instancia
```

### Conectar à instância

```bash
# Instalar o Cloud SQL Auth Proxy (recomendado para conexão local)
# Linux/macOS
curl -o cloud-sql-proxy https://storage.googleapis.com/cloud-sql-connectors/cloud-sql-proxy/v2.8.0/cloud-sql-proxy.linux.amd64
chmod +x cloud-sql-proxy

# Rodar o proxy (em outro terminal)
./cloud-sql-proxy MEU_PROJETO:southamerica-east1:minha-instancia

# Conectar via psql depois que o proxy estiver rodando
psql "host=127.0.0.1 port=5432 dbname=meu-banco user=postgres"

# Conectar diretamente via gcloud (sem instalar psql localmente)
gcloud sql connect minha-instancia \
  --user=postgres \
  --database=meu-banco

# Ver o Connection Name da instância (necessário para o proxy)
gcloud sql instances describe minha-instancia \
  --format="value(connectionName)"
```

### Autorizar IPs externos

> **⚠️ Atenção:** liberar IP público tem implicações de segurança. Use Cloud SQL Proxy ou Private IP quando possível.

```bash
# Adicionar IP autorizado para acesso externo
gcloud sql instances patch minha-instancia \
  --authorized-networks="SEU_IP/32"

# Adicionar múltiplos IPs
gcloud sql instances patch minha-instancia \
  --authorized-networks="IP1/32,IP2/32"

# Remover todos os IPs autorizados
gcloud sql instances patch minha-instancia \
  --clear-authorized-networks
```

### Backups e restauração

```bash
# Criar backup manual imediato
gcloud sql backups create \
  --instance=minha-instancia \
  --description="Backup antes do deploy"

# Listar backups disponíveis
gcloud sql backups list \
  --instance=minha-instancia

# Restaurar de um backup (⚠️ sobrescreve os dados atuais)
gcloud sql backups restore BACKUP_ID \
  --restore-instance=minha-instancia \
  --backup-instance=minha-instancia

# Exportar banco para Cloud Storage (SQL dump)
gcloud sql export sql minha-instancia \
  gs://meu-bucket/backup-$(date +%Y%m%d).sql \
  --database=meu-banco

# Importar de um dump no Cloud Storage
gcloud sql import sql minha-instancia \
  gs://meu-bucket/backup.sql \
  --database=meu-banco
```

### Gerenciar instância

```bash
# Parar instância (para economizar custo em dev)
gcloud sql instances patch minha-instancia \
  --activation-policy=NEVER

# Iniciar instância
gcloud sql instances patch minha-instancia \
  --activation-policy=ALWAYS

# Deletar instância (⚠️ irreversível)
gcloud sql instances delete minha-instancia

# Redimensionar storage (só aumenta, não diminui)
gcloud sql instances patch minha-instancia \
  --storage-size=20GB

# Trocar tier (redimensionar CPU/RAM)
gcloud sql instances patch minha-instancia \
  --tier=db-n1-standard-2
```

---

## 🔑 Secrets Manager

> **O que é:** serviço para armazenar com segurança senhas, chaves de API, strings de conexão e outros dados sensíveis. Nunca coloque credenciais em variáveis de ambiente diretamente no código ou no repositório — use o Secrets Manager.

### Criar e gerenciar secrets

```bash
# Criar um secret (apenas o "cofre", sem valor ainda)
gcloud secrets create minha-senha-db \
  --replication-policy=automatic

# Criar secret e já adicionar o valor
echo -n "minha_senha_super_secreta" | \
  gcloud secrets create minha-senha-db \
  --data-file=-

# Criar secret a partir de um arquivo
gcloud secrets create meu-certificado \
  --data-file=./cert.pem

# Criar secret com valor diretamente (menos seguro, fica no histórico do shell)
gcloud secrets create api-key \
  --replication-policy=automatic
# Adicionar versão depois:
echo -n "sk-abc123xyz" | gcloud secrets versions add api-key --data-file=-

# Listar todos os secrets do projeto
gcloud secrets list

# Ver detalhes de um secret (sem revelar o valor)
gcloud secrets describe minha-senha-db
```

### Versões de secrets

> **O que é versão:** cada vez que você atualiza um secret, uma nova versão é criada. Versões antigas ficam disponíveis para rollback. `latest` sempre aponta para a versão mais recente.

```bash
# Adicionar nova versão (atualizar valor)
echo -n "nova_senha_atualizada" | \
  gcloud secrets versions add minha-senha-db \
  --data-file=-

# Listar versões de um secret
gcloud secrets versions list minha-senha-db

# LER o valor de um secret (versão mais recente)
gcloud secrets versions access latest \
  --secret=minha-senha-db

# LER uma versão específica
gcloud secrets versions access 2 \
  --secret=minha-senha-db

# Desabilitar uma versão (sem deletar, para rollback)
gcloud secrets versions disable 1 \
  --secret=minha-senha-db

# Reabilitar uma versão desabilitada
gcloud secrets versions enable 1 \
  --secret=minha-senha-db

# Destruir uma versão (permanente, sem recuperação)
gcloud secrets versions destroy 1 \
  --secret=minha-senha-db
```

### Deletar secrets

```bash
# Deletar um secret e todas as suas versões
gcloud secrets delete minha-senha-db

# Deletar sem confirmação interativa (para scripts)
gcloud secrets delete minha-senha-db --quiet
```

### Permissões para acessar secrets

> Antes de uma aplicação (Cloud Run, Cloud Function etc.) acessar um secret, a service account dela precisa de permissão.

```bash
# Dar permissão de leitura para uma Service Account
gcloud secrets add-iam-policy-binding minha-senha-db \
  --member="serviceAccount:minha-sa@MEU_PROJETO.iam.gserviceaccount.com" \
  --role="roles/secretmanager.secretAccessor"

# Dar permissão para a SA padrão do Cloud Run
# (descubra a SA padrão com o comando abaixo)
gcloud run services describe meu-servico \
  --region=southamerica-east1 \
  --format="value(spec.template.spec.serviceAccountName)"

# Dar acesso à conta de serviço padrão do Compute Engine
gcloud secrets add-iam-policy-binding minha-senha-db \
  --member="serviceAccount:NUMERO_PROJETO-compute@developer.gserviceaccount.com" \
  --role="roles/secretmanager.secretAccessor"
```

### Usar secrets no código (Node.js)

```javascript
// npm install @google-cloud/secret-manager
const { SecretManagerServiceClient } = require('@google-cloud/secret-manager');
const client = new SecretManagerServiceClient();

async function getSecret(secretName) {
  const [version] = await client.accessSecretVersion({
    name: `projects/MEU_PROJETO/secrets/${secretName}/versions/latest`,
  });
  return version.payload.data.toString('utf8');
}

// Uso
const dbPassword = await getSecret('minha-senha-db');
```

### Usar secrets no código (Python)

```python
# pip install google-cloud-secret-manager
from google.cloud import secretmanager

def get_secret(secret_name: str, project_id: str) -> str:
    client = secretmanager.SecretManagerServiceClient()
    name = f"projects/{project_id}/secrets/{secret_name}/versions/latest"
    response = client.access_secret_version(request={"name": name})
    return response.payload.data.decode("utf-8")

# Uso
db_password = get_secret("minha-senha-db", "meu-projeto")
```

---

## 🛡️ IAM — Permissões Essenciais

> **O que é IAM:** Identity and Access Management. Define quem pode fazer o quê nos seus recursos do GCP. Sem a permissão certa, nenhum serviço funciona.

```bash
# Ver as permissões de um projeto
gcloud projects get-iam-policy MEU_PROJETO

# Dar um papel (role) a um usuário
gcloud projects add-iam-policy-binding MEU_PROJETO \
  --member="user:email@exemplo.com" \
  --role="roles/editor"

# Dar papel a uma Service Account
gcloud projects add-iam-policy-binding MEU_PROJETO \
  --member="serviceAccount:minha-sa@MEU_PROJETO.iam.gserviceaccount.com" \
  --role="roles/run.invoker"

# Remover permissão
gcloud projects remove-iam-policy-binding MEU_PROJETO \
  --member="user:email@exemplo.com" \
  --role="roles/editor"

# Roles mais comuns:
# roles/viewer          → somente leitura
# roles/editor          → leitura e escrita (cuidado!)
# roles/owner           → controle total
# roles/run.invoker     → pode invocar Cloud Run
# roles/run.developer   → pode fazer deploy no Cloud Run
# roles/cloudfunctions.invoker → pode chamar Cloud Functions
# roles/storage.objectViewer   → lê objetos no Storage
# roles/storage.objectAdmin    → CRUD em objetos
# roles/cloudsql.client        → conecta ao Cloud SQL
# roles/secretmanager.secretAccessor → lê secrets
```

---

## 📊 Logs e Monitoramento

```bash
# Ver logs de qualquer recurso
gcloud logging read "resource.type=cloud_run_revision" \
  --limit=50 \
  --format="table(timestamp,severity,textPayload)"

# Filtrar por severidade
gcloud logging read \
  "severity>=ERROR AND resource.type=cloud_run_revision" \
  --limit=20

# Ver logs das últimas 2 horas
gcloud logging read \
  "resource.type=cloud_run_revision" \
  --freshness=2h

# Ver logs de auditoria (quem fez o quê)
gcloud logging read \
  "logName=projects/MEU_PROJETO/logs/cloudaudit.googleapis.com%2Factivity" \
  --limit=20

# Exportar logs para arquivo
gcloud logging read \
  "resource.type=cloud_run_revision" \
  --limit=1000 \
  --format=json > logs.json
```

---

## 📌 Cheat Sheet — Referência Rápida

### Auth

```bash
gcloud auth login                          # Login interativo
gcloud auth application-default login     # ADC para SDKs locais
gcloud auth list                           # Ver contas autenticadas
```

### Config

```bash
gcloud config set project MEU_PROJETO     # Setar projeto
gcloud config set compute/region southamerica-east1
gcloud config list                         # Ver config atual
gcloud config configurations activate prod # Trocar ambiente
```

### Cloud Run

```bash
gcloud run deploy NOME --image=IMG --region=REGIAO --allow-unauthenticated
gcloud run services list --region=REGIAO
gcloud run services describe NOME --region=REGIAO --format="value(status.url)"
gcloud run services logs tail NOME --region=REGIAO
gcloud run services delete NOME --region=REGIAO
```

### Cloud Functions

```bash
gcloud functions deploy NOME --gen2 --runtime=nodejs20 --trigger-http --allow-unauthenticated
gcloud functions list --region=REGIAO
gcloud functions describe NOME --region=REGIAO
gcloud functions call NOME --data='{"key":"value"}'
gcloud functions delete NOME --region=REGIAO
```

### Cloud Storage

```bash
gcloud storage buckets create gs://BUCKET --location=REGIAO
gcloud storage cp ARQUIVO gs://BUCKET/
gcloud storage cp -r gs://BUCKET/PASTA/ ./LOCAL/
gcloud storage ls gs://BUCKET/
gcloud storage rm gs://BUCKET/ARQUIVO
gcloud storage du gs://BUCKET/ --summarize
```

### Cloud SQL

```bash
gcloud sql instances create NOME --database-version=POSTGRES_15 --tier=db-f1-micro --region=REGIAO
gcloud sql users set-password postgres --instance=NOME --password=SENHA
gcloud sql databases create BANCO --instance=NOME
gcloud sql connect NOME --user=postgres
gcloud sql instances patch NOME --activation-policy=NEVER   # parar instância
```

### Secrets Manager

```bash
echo -n "VALOR" | gcloud secrets create NOME --data-file=-
echo -n "NOVO_VALOR" | gcloud secrets versions add NOME --data-file=-
gcloud secrets versions access latest --secret=NOME
gcloud secrets list
gcloud secrets delete NOME
```

---

## 🔥 Erros Comuns e Soluções

| Erro | Causa | Solução |
|------|-------|---------|
| `ERROR: (gcloud.run.deploy) PERMISSION_DENIED` | SA sem permissão | Adicione `roles/run.developer` à sua conta |
| `API [run.googleapis.com] not enabled` | API não habilitada | `gcloud services enable run.googleapis.com` |
| `The region X does not support Cloud Run` | Região errada | Use `southamerica-east1` para Brasil |
| `FAILED_PRECONDITION: Cloud SQL instance not running` | Instância parada | `gcloud sql instances patch NOME --activation-policy=ALWAYS` |
| `403 Forbidden` no Cloud Storage | Sem permissão | Adicione `roles/storage.objectAdmin` à SA |
| `Secret not found` | Secret não existe ou projeto errado | Verifique `gcloud config get-value project` |
| `Quota exceeded` | Limite de recursos atingido | Solicite aumento em console.cloud.google.com/iam-admin/quotas |
| `UNAUTHENTICATED: Request had invalid authentication credentials` | Credenciais expiradas | `gcloud auth login` novamente |
| `ERROR: (gcloud) You do not currently have an active account` | Não logado | `gcloud auth login` |
| `Permission 'secretmanager.versions.access' denied` | SA sem acesso | `gcloud secrets add-iam-policy-binding` com `secretAccessor` |

---

## 💡 Boas Práticas

### Segurança

```bash
# ✅ Sempre use Secrets Manager para credenciais
echo -n "minha_senha" | gcloud secrets create db-pass --data-file=-

# ✅ Nunca use --allow-unauthenticated em produção para APIs internas
# Use IAM ou API Gateway com autenticação

# ✅ Dê permissões mínimas necessárias (princípio do menor privilégio)
# Evite roles/editor ou roles/owner para SAs de aplicações

# ✅ Rotacione segredos periodicamente
echo -n "nova_senha" | gcloud secrets versions add db-pass --data-file=-

# ✅ Use Private IP para Cloud SQL em produção
# Evite liberar IPs públicos sem necessidade
```

### Custo

```bash
# ✅ Use min-instances=0 no Cloud Run para escalar até zero
gcloud run services update NOME --min-instances=0

# ✅ Pause instâncias Cloud SQL que não usa (dev/staging)
gcloud sql instances patch NOME --activation-policy=NEVER

# ✅ Use Nearline/Coldline para dados raramente acessados
gcloud storage buckets create gs://BUCKET --default-storage-class=NEARLINE

# ✅ Configure lifecycle para deletar objetos antigos automaticamente
gcloud storage buckets update gs://BUCKET --lifecycle-file=lifecycle.json
```

### Produtividade

```bash
# ✅ Use configurações nomeadas para alternar ambientes rapidamente
gcloud config configurations activate prod

# ✅ Use --format para extrair valores em scripts
URL=$(gcloud run services describe NOME --region=REGIAO --format="value(status.url)")

# ✅ Use --quiet para scripts sem confirmação interativa
gcloud sql instances delete NOME --quiet

# ✅ Exporte PROJECT_ID como variável de ambiente no seu shell
export PROJECT_ID=$(gcloud config get-value project)
export REGION=southamerica-east1
```

---

## 🌎 Regiões GCP no Brasil e América do Sul

| Região | Localização | Latência (BR) | Uso recomendado |
|--------|------------|:-------------:|----------------|
| `southamerica-east1` | São Paulo, BR | ⚡ Muito baixa | Produção BR |
| `southamerica-west1` | Santiago, CL | ⚡ Baixa | Fallback/DR |
| `us-central1` | Iowa, EUA | 🔶 Média | Dev/Staging |
| `us-east1` | Carolina do Sul, EUA | 🔶 Média | Dev/Staging |

```bash
# Para aplicações com usuários brasileiros, sempre use:
gcloud config set compute/region southamerica-east1
```

---

## 📚 Recursos Adicionais

| Recurso | Link |
|---------|------|
| Documentação oficial gcloud CLI | https://cloud.google.com/sdk/gcloud/reference |
| Cloud Run — Quickstart | https://cloud.google.com/run/docs/quickstarts |
| Cloud Functions — Guia | https://cloud.google.com/functions/docs |
| Cloud SQL — Conexão com Proxy | https://cloud.google.com/sql/docs/postgres/sql-proxy |
| Secrets Manager — Guia | https://cloud.google.com/secret-manager/docs |
| Cloud Storage — Guia | https://cloud.google.com/storage/docs |
| Calculadora de preços GCP | https://cloud.google.com/products/calculator |
| Free Tier GCP | https://cloud.google.com/free |

---

## 🤝 Contribuindo

Se encontrou um comando desatualizado, erro ou quer adicionar um novo serviço:

1. Abra uma **Issue** descrevendo o problema ou sugestão
2. Faça um **PR** com a correção e fonte da documentação oficial
3. Inclua a versão do gcloud CLI testada (`gcloud version`)

---

<div align="center">

**Feito para quem está começando no GCP e quer ir direto ao ponto** ☁️

*Se foi útil, deixa uma ⭐ no repositório!*

</div>
