
# colocar etiquetas em contatos via webhook no chatwoot pela automação n8n exemplo

Para adicionar etiquetas (labels) a contatos no Chatwoot via webhook no n8n, configure um workflow que recebe o payload do webhook do Chatwoot (com ID do contato), processa a lógica (ex: IA ou regra) e envia POST para a API Chatwoot. O endpoint é `POST /api/v1/accounts/{account_id}/contacts/{contact_id}/labels`, que substitui as labels existentes pelo array fornecido.[^4_1]

## Exemplo de Workflow n8n

1. Nó **Webhook**: Recebe evento do Chatwoot (ex: `contact_created` ou `message_created`), extraindo `contact.id` e `account.id` do payload.[^4_1]
2. Nó opcional **OpenAI** ou **IF**: Analisa mensagem para decidir labels (ex: "vip" se menciona "premium").[^4_2]
3. Nó **HTTP Request** para adicionar labels:
```
Method: POST
URL: https://app.chatwoot.com/api/v1/accounts/{{ $json.account_id }}/contacts/{{ $json.contact.id }}/labels
Headers:
  Content-Type: application/json
  api_access_token: SUA_API_KEY_CHATWOOT
Body (JSON):
{
  "labels": ["vip", "suporte", "internacional"]
}
```


## Payload Webhook Chatwoot

Exemplo recebido no n8n:

```
{
  "event": "message_created",
  "contact": { "id": 123, "name": "João" },
  "account_id": 456
}
```

Use expressões n8n como `{{$json.contact.id}}` para dinamizar.[^4_1]

## Dicas Práticas

- Gere `api_access_token` no perfil de agente Chatwoot (Settings > Profile).
- Crie labels antes no Chatwoot (Settings > Labels).[^4_2]
- Para conversas (não contatos), use `/conversations/{conversation_id}/labels`.
- Teste com webhook de teste no Chatwoot (Settings > Integrations > Webhooks). Integre com Evolution API para WhatsApp, como em seus fluxos anteriores.[^4_3][^4_1]
<span style="display:none">[^4_10][^4_11][^4_12][^4_4][^4_5][^4_6][^4_7][^4_8][^4_9]</span>

<div align="center">⁂</div>

[^4_1]: https://www.chatwoot.com/hc/user-guide/articles/1677693021-how-to-use-webhooks

[^4_2]: interests.web_development.same_origin_policy

[^4_3]: https://www.youtube.com/watch?v=MYwLQEvyzZk

[^4_4]: https://www.perplexity.ai/search/f9d3ffb2-e866-4bf7-a8e9-e4ae34c01d63

[^4_5]: https://github.com/chatwoot/chatwoot/commit/a2d6fa0f74d84ce9d8f816bec69098be40d5083f

[^4_6]: https://www.youtube.com/watch?v=S5auddqGcKY

[^4_7]: https://developers.chatwoot.com/api-reference/contact-labels/add-labels

[^4_8]: https://www.youtube.com/watch?v=6kZ8tSZtGKE

[^4_9]: https://www.youtube.com/watch?v=aMmRmeGstz8

[^4_10]: https://developers.chatwoot.com/api-reference/conversations/add-labels

[^4_11]: https://www.youtube.com/watch?v=bf7ntP_DEpQ

[^4_12]: https://n8n.io/integrations/webhook/


---

# Chamada HTTP em n8n para endpoint de labels no Chatwoot

Configure o nó **HTTP Request** no n8n para adicionar labels no Chatwoot com os parâmetros exatos abaixo.

## Configuração HTTP Request

```
Method: POST
URL: https://app.chatwoot.com/api/v1/accounts/{{$node["Webhook"].json["account_id"]}}/contacts/{{$node["Webhook"].json["contact"]["id"]}}/labels

Headers:
- Name: api_access_token
  Value: SEU_TOKEN_CHATWOOT
- Name: Content-Type  
  Value: application/json

Body (JSON):
{
  "labels": ["vip", "suporte", "pendente"]
}
```


## Exemplo Completo Workflow

```
1. Webhook (recebe evento Chatwoot)
   ↓
2. Set (extrai IDs)
   - account_id: {{$json.account_id}}
   - contact_id: {{$json.contact.id}}
   ↓  
3. HTTP Request (config acima)
```


## Autenticação Correta

Use **api_access_token** no header (não Bearer). Gere em Chatwoot > Settings > Profile > API Access Token. Labels substituem as existentes - envie array vazio `[]` para remover todas.[^5_2]

## Teste Rápido

```
curl -X POST "https://app.chatwoot.com/api/v1/accounts/123/contacts/456/labels" \
-H "api_access_token: abc123" \
-H "Content-Type: application/json" \
-d '{"labels":["teste"]}'
```

Retorna `200 OK` se sucesso. Para conversas, troque `/contacts/` por `/conversations/".[page:0]
<span style="display:none">[^5_1][^5_10][^5_11][^5_3][^5_4][^5_5][^5_6][^5_7][^5_8][^5_9]</span>

<div align="center">⁂</div>

[^5_1]: https://community.n8n.io/t/chatwoot-and-n8n-http-conection-error/68907

[^5_2]: https://community.n8n.io/t/cahtwoot-api/235896

[^5_3]: https://horadecodar.com.br/redirecionar-chatwoot-whatsapp-evolution-api-n8n-midia/

[^5_4]: https://www.youtube.com/watch?v=u3T0j8pR6lw

[^5_5]: https://www.youtube.com/watch?v=f889nl2YjYE

[^5_6]: https://github.com/chatwoot/chatwoot/issues/12792

[^5_7]: https://www.youtube.com/watch?v=6kZ8tSZtGKE

[^5_8]: https://www.chatwoot.com/hc/user-guide/en/categories/apps-and-integrations

[^5_9]: https://www.youtube.com/watch?v=CMOFHvZeWDg

[^5_10]: https://n8n.io/integrations/http-request/

[^5_11]: interests.web_development.same_origin_policy

