# 🐳 MCP BRU IA – Docker Deployment Guide
_MCP BRU IA – Docker Build, Push & Deploy Documentation_

<p align="center">
  <a href="https://www.docker.com/" target="_blank" title="Docker">
    <img src="https://img.shields.io/badge/Docker-Containerization-2496ED?logo=docker&logoColor=white" alt="Docker Badge"/>
  </a>
  <a href="https://nodejs.org/" target="_blank" title="Node.js">
    <img src="https://img.shields.io/badge/Node.js-Runtime-339933?logo=node.js&logoColor=white" alt="Node.js Badge"/>
  </a>
  <a href="https://hub.docker.com/" target="_blank" title="Docker Hub">
    <img src="https://img.shields.io/badge/DockerHub-Image%20Registry-0db7ed?logo=docker&logoColor=white" alt="DockerHub Badge"/>
  </a>
  <a href="https://github.com/brunopelatieri/mcp-bru-ia" target="_blank" title="GitHub Repository">
    <img src="https://img.shields.io/badge/GitHub-mcp--bru--ia-181717?logo=github&logoColor=white" alt="GitHub Badge"/>
  </a>
</p>

---

# 🇧🇷 Guia Completo de Build, Push e Deploy

Este documento centraliza todos os comandos necessários para:

- Construir imagens Docker
- Publicar no Docker Hub
- Limpar imagens antigas na VPS
- Validar ambiente Node antes do build
- Garantir consistência entre desenvolvimento e produção

---

## 🔐 Login no Docker Hub

```bash
docker login
```

---

## 🏗️ Build da Imagem

### Build padrão

```bash
docker build -t brunopelatieri/mcp-bru-ia:2.0.0 .
```

### Build forçando limpeza de cache (recomendado)

```bash
docker build --no-cache -t brunopelatieri/mcp-bru-ia:3.1.1 .
```

---

## 🚀 Enviar imagem para Docker Hub

```bash
docker push brunopelatieri/mcp-bru-ia:3.1.1
```

---

## 🧹 Limpeza Completa na VPS

```bash
docker rmi brunopelatieri/mcp-bru-ia:1.0.0 --force
docker pull brunopelatieri/mcp-bru-ia:1.0.0
```

---

## 🧪 Teste Manual da Imagem

```bash
docker run --rm brunopelatieri/mcp-bru-ia:1.0.0 node index.js
```

---

## 📜 Logs

### Logs container

```bash
docker logs -f mcp
```

### Logs service (Docker Swarm)

```bash
docker service logs bmcp_smcp -f
```

---

```bash
docker service logs mcp-bru_app --no-trunc --tail 20 2>&1
```

---

## ⚙️ Execução Manual com Variáveis

```bash
docker run --rm \
  -e PORT=3000 \
  -e N8N_URL=SEU_VALOR \
  -e N8N_API_KEY=SEU_VALOR \
  brunopelatieri/mcp-bru-ia:2.2.0 \
  node index.js
```

---

## 🔎 Verificar Secrets

```bash
docker secret ls
```

---

## 🌐 Verificar Redes Docker

```bash
docker network ls
```

---

# 📦 Sincronização de Dependências Node.js

## Gerar / Atualizar Lockfile

```bash
npm install
```

## Validar Dependências

```bash
npm list
```

## Auditoria de Segurança

```bash
npm audit
```

---

# 🚨 Importante: package-lock.json

Não ignore o arquivo `package-lock.json` no `.gitignore`.

No Dockerfile:

```dockerfile
COPY package*.json ./
```

Isso garante que o container instale exatamente as versões testadas.

---

# 🔄 Atualizar Remote do Git

```bash
git remote set-url origin git@github.com:brunopelatieri/mcp-bru-ia.git
```

---

# 🇺🇸 Complete Build, Push & Deploy Guide

This document centralizes all commands required to:

- Build Docker images  
- Push to Docker Hub  
- Clean old VPS images  
- Validate Node environment before build  
- Ensure production consistency  

---

## Docker Login

```bash
docker login
```

---

## Build Image

```bash
docker build -t brunopelatieri/mcp-bru-ia:2.0.0 .
```

### Force clean build

```bash
docker build --no-cache -t brunopelatieri/mcp-bru-ia:3.1.1 .
```

---

## Push to Docker Hub

```bash
docker push brunopelatieri/mcp-bru-ia:3.1.1
```

---

## Clean VPS Old Image

```bash
docker rmi brunopelatieri/mcp-bru-ia:1.0.0 --force
docker pull brunopelatieri/mcp-bru-ia:1.0.0
```

---

## Manual Container Test

```bash
docker run --rm brunopelatieri/mcp-bru-ia:1.0.0 node index.js
```

---

# 👤 Author

Bruno Pelatieri Goulart  
DevOps • AI Integration • Docker Automation • Node.js
