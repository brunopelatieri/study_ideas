# 📋 Extração de PDF com Gemini no n8n

> Base de conhecimento para integração entre **Evolution API**, **n8n** e **Google Gemini** para extração estruturada de dados via imagem ou PDF.

---

## 📌 Índice

- [📋 Extração de PDF com Gemini no n8n](#-extração-de-pdf-com-gemini-no-n8n)
  - [📌 Índice](#-índice)
  - [🧩 Contexto do Problema](#-contexto-do-problema)
    - [O problema com PDF no nó nativo](#o-problema-com-pdf-no-nó-nativo)
    - [Por que a imagem funciona e o PDF não?](#por-que-a-imagem-funciona-e-o-pdf-não)
  - [✅ Solução: HTTP Request direto ao Gemini](#-solução-http-request-direto-ao-gemini)
  - [🔁 Fluxo no n8n](#-fluxo-no-n8n)
  - [🧹 Nó Code — Limpeza da Base64](#-nó-code--limpeza-da-base64)
  - [🌐 Nó HTTP Request — Gemini API](#-nó-http-request--gemini-api)
    - [Configuração do nó](#configuração-do-nó)
    - [Body da requisição](#body-da-requisição)
    - [Exemplo com prompt fixo no body](#exemplo-com-prompt-fixo-no-body)
  - [📦 Nó Code — Extração da Resposta](#-nó-code--extração-da-resposta)
  - [🖼️ Suporte a Imagens](#️-suporte-a-imagens)
  - [🔍 Detecção Automática do Tipo de Arquivo](#-detecção-automática-do-tipo-de-arquivo)
  - [📎 Referência de mime\_type](#-referência-de-mime_type)
  - [⚠️ Observações Importantes](#️-observações-importantes)
  - [👤 Autor](#-autor)

---

## 🧩 Contexto do Problema

Em automações que recebem **PDFs ou imagens de PDF** via **Evolution API** (WhatsApp), o arquivo chega em **base64**. O fluxo típico no n8n converte esse base64 para binário e envia ao modelo via **nó nativo de AI Agent**.

### O problema com PDF no nó nativo

O nó nativo do **AI Agent** no n8n processa o PDF extraindo seu texto antes de enviá-lo ao modelo. Isso causa um efeito colateral crítico em PDFs com fontes customizadas ou estilizadas:

```
# Texto que o modelo recebe (errado):
P A L I T O S  D E  M O Z A R E L L A / R $ 2 0 , 0 0

# Texto que deveria receber (correto):
PALITOS DE MOZARELLA / R$ 20,00
```

Cada caractere separado por espaço faz o modelo falhar ao identificar nomes de produtos e preços, quebrando toda a extração estruturada.

### Por que a imagem funciona e o PDF não?

| Entrada | Como o nó nativo processa | Resultado |
|---|---|---|
| Imagem (PNG/JPG) | Envia os pixels diretamente | ✅ Funciona |
| PDF | Extrai texto antes de enviar | ❌ Texto corrompido |

---

## ✅ Solução: HTTP Request direto ao Gemini

Ao invés de usar o nó nativo do AI Agent para processar o arquivo, fazemos uma chamada **HTTP Request direta à API do Gemini**, enviando o arquivo como `inline_data` em base64. Dessa forma o Gemini **renderiza o PDF visualmente**, exatamente como faz com imagens, eliminando o problema de extração de texto.

---

## 🔁 Fluxo no n8n

```
Evolution API (base64)
        ↓
Nó Code — Limpeza da base64 + detecção do tipo
        ↓
Nó HTTP Request — Gemini API (inline_data)
        ↓
Nó Code — Extração e parse do JSON retornado
        ↓
Próximos nós do fluxo (AI Agent, banco de dados, etc.)
```

---

## 🧹 Nó Code — Limpeza da Base64

Adicione um nó **Code** (JavaScript) para limpar o prefixo do data URI e detectar o tipo do arquivo:

```javascript
const raw = $input.first().json.message.base64; // ajuste conforme seu campo

// Remove prefixo data URI se presente (ex: "data:image/png;base64,")
const base64Clean = raw.includes(',') ? raw.split(',')[1] : raw;

// Detecta o mime_type pelo prefixo
let mimeType = 'image/jpeg'; // padrão
if (raw.includes('data:application/pdf'))  mimeType = 'application/pdf';
else if (raw.includes('data:image/png'))   mimeType = 'image/png';
else if (raw.includes('data:image/webp'))  mimeType = 'image/webp';
else if (raw.includes('data:image/jpeg'))  mimeType = 'image/jpeg';

return [{ json: { base64Clean, mimeType } }];
```

> **Ajuste o campo** `$input.first().json.message.base64` conforme a estrutura do payload recebido da Evolution API no seu fluxo.

---

## 🌐 Nó HTTP Request — Gemini API

### Configuração do nó

| Campo | Valor |
| --- | --- |
| **Method** | `POST` |
| **URL** | `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=SUA_API_KEY` |
| **Content-Type** | `application/json` |
| **Body** | JSON (ver abaixo) |

> Substitua `SUA_API_KEY` pela sua chave da [Google AI Studio](https://aistudio.google.com/apikey).  
> Substitua `gemini-2.0-flash` pelo modelo desejado, ex: `gemini-2.5-flash-preview-05-20`.

### Body da requisição

```json
{
  "contents": [
    {
      "parts": [
        {
          "inline_data": {
            "mime_type": "={{ $json.mimeType }}",
            "data": "={{ $json.base64Clean }}"
          }
        },
        {
          "text": "={{ $json.prompt }}"
        }
      ]
    }
  ],
  "generationConfig": {
    "temperature": 0.05
  }
}
```

> O campo `text` pode receber o prompt diretamente como string fixa ou via expressão n8n apontando para um campo que contenha o prompt.

### Exemplo com prompt fixo no body

```json
{
  "contents": [
    {
      "parts": [
        {
          "inline_data": {
            "mime_type": "={{ $json.mimeType }}",
            "data": "={{ $json.base64Clean }}"
          }
        },
        {
          "text": "Analise este cardápio e retorne um JSON estruturado com todos os produtos, preços e categorias encontrados."
        }
      ]
    }
  ],
  "generationConfig": {
    "temperature": 0.05
  }
}
```

---

## 📦 Nó Code — Extração da Resposta

Após o HTTP Request, adicione um nó **Code** para extrair e fazer o parse do JSON retornado pelo Gemini:

```javascript
const raw = $input.first().json.candidates[0].content.parts[0].text;

// Remove blocos de markdown se o modelo retornar ```json ... ```
const clean = raw.replace(/```json|```/g, '').trim();

const parsed = JSON.parse(clean);

return [{ json: { result: parsed } }];
```

> O resultado `result` conterá o JSON estruturado pronto para ser usado nos próximos nós do fluxo.

---

## 🖼️ Suporte a Imagens

O mesmo fluxo funciona para imagens. A única diferença é o `mime_type` no body do HTTP Request:

```json
{
  "contents": [
    {
      "parts": [
        {
          "inline_data": {
            "mime_type": "image/png",
            "data": "={{ $json.base64Clean }}"
          }
        },
        {
          "text": "SEU PROMPT AQUI"
        }
      ]
    }
  ]
}
```

---

## 🔍 Detecção Automática do Tipo de Arquivo

Usando o nó Code de limpeza com detecção automática (mostrado acima), o mesmo fluxo trata qualquer tipo de entrada sem alterações:

```text
PDF recebido    → mime_type: application/pdf  → Gemini renderiza visualmente ✅
PNG recebido    → mime_type: image/png        → Gemini lê a imagem ✅
JPG recebido    → mime_type: image/jpeg       → Gemini lê a imagem ✅
WEBP recebido   → mime_type: image/webp       → Gemini lê a imagem ✅
```

---

## 📎 Referência de mime_type

| Formato | mime_type |
| --- | --- |
| PDF | `application/pdf` |
| JPEG / JPG | `image/jpeg` |
| PNG | `image/png` |
| WEBP | `image/webp` |
| GIF | `image/gif` |

---

## ⚠️ Observações Importantes

- O nó nativo **AI Agent** do n8n **não renderiza PDFs visualmente** — ele extrai o texto, o que causa corrupção em PDFs com fontes customizadas. Use sempre o HTTP Request direto para arquivos.
- A base64 enviada ao Gemini deve ser **pura**, sem o prefixo `data:mimetype;base64,`. O nó Code de limpeza já trata isso.
- O modelo `gemini-2.0-flash` aceita PDFs de até **1000 páginas** e imagens de até **20MB** via `inline_data`.
- Mantenha `temperature` baixo (recomendado `0.05`) para respostas mais determinísticas em extração de dados estruturados.
- Sempre envolva o parse do JSON em `try/catch` em produção para tratar respostas inesperadas do modelo.

```javascript
// Versão segura do nó de extração
try {
  const raw = $input.first().json.candidates[0].content.parts[0].text;
  const clean = raw.replace(/```json|```/g, '').trim();
  const parsed = JSON.parse(clean);
  return [{ json: { result: parsed, status: 'success' } }];
} catch (err) {
  return [{ json: { result: null, status: 'error', message: err.message } }];
}
```

---

## 👤 Autor

Bruno Pelatieri Goulart

---

> 📅 Documentação gerada como base de conhecimento para desenvolvimento de automações com n8n + Google Gemini + Evolution API.
