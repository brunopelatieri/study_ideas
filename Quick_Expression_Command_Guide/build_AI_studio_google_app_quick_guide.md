# 🚀 Google AI Studio Landing Page Export
_Project Build & Deployment Guide_

<p align="center">
  <a href="https://ai.google.dev/" target="_blank" title="Google AI Studio">
    <img src="https://img.shields.io/badge/Google%20AI%20Studio-Project-4285F4?logo=google&logoColor=white" alt="Google AI Studio Badge"/>
  </a>
  <a href="https://nodejs.org/" target="_blank" title="Node.js">
    <img src="https://img.shields.io/badge/Node.js-Runtime-339933?logo=node.js&logoColor=white" alt="Node.js Badge"/>
  </a>
  <a href="https://www.npmjs.com/" target="_blank" title="NPM">
    <img src="https://img.shields.io/badge/npm-Package%20Manager-CB3837?logo=npm&logoColor=white" alt="NPM Badge"/>
  </a>
</p>

---

## 🇧🇷 Descrição

Este repositório contém o código-fonte de uma **Landing Page exportada do Google AI Studio**.

O projeto utiliza ambiente Node.js para gerenciamento de dependências e geração de build final para produção.

---

## 🇺🇸 Description

This repository contains the source code of a **Landing Page exported from Google AI Studio**.

The project uses Node.js for dependency management and production build generation.

---

# 🛠️ Instalação e Execução | Installation & Usage

## 1️⃣ Instalar Dependências | Install Dependencies

```bash
npm install
```

---

## 2️⃣ Ambiente de Desenvolvimento | Development Mode

```bash
npm run dev
```

Inicia o servidor local para desenvolvimento.

---

## 3️⃣ Gerar Build de Produção | Production Build

```bash
npm run build
```

Este comando gera a pasta:

```
dist/
```

Os arquivos dentro desta pasta devem ser enviados para o servidor.

---

# 🌐 Deploy no Zar Hosting | Deployment Guide

### 🇧🇷 Passos:

1. Execute `npm run build`
2. Acesse a pasta `dist`
3. Compacte os arquivos
4. Envie para o diretório raiz do servidor (ex: `public_html`)

---

### 🇺🇸 Steps:

1. Run `npm run build`
2. Open the `dist` folder
3. Compress the files
4. Upload to your server root directory (e.g., `public_html`)

---

# ⚠️ Observação Importante (Windows / PowerShell)

O comando:

```bash
cat <<EOF
```

Não funciona no PowerShell do Windows.

Para criar arquivos README no Windows:

- Utilize VS Code ou outro editor
- Crie manualmente o arquivo `README.md`
- Cole o conteúdo
- Salve normalmente

---

# 📦 Estrutura Esperada do Projeto

```
project-root/
├── src/
├── public/
├── package.json
├── README.md
└── dist/ (gerado após build)
```

---

# 📌 Boas Práticas

✔ Sempre executar `npm install` antes do build  
✔ Não subir a pasta `node_modules` para produção  
✔ Fazer upload apenas do conteúdo da pasta `dist`  
✔ Testar localmente antes do deploy  

---

# 👤 Autor

Bruno Pelatieri Goulart  
AI Systems • Web Architecture • Automation

---

© 2026 - Documentação técnica estruturada para deploy seguro.
