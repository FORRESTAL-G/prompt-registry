# Premessa:
Ho voluto incollare la parte incriminante, cosí come si trova nello stato attuale nel workflow:
1. Avviene il recupero dell'intera row dati user tramite chatID;
2. Controlliamo con un if se il json ci viene restituito, quindi
   1. Se viene restituiamo il un oggetto valido, non null, andiamo su oroute del meccanismo di concorrenza asincrona, altrimenti;
   2. Se l'oggetto é un1defined, nullo o "" andiamo verso la registrazione.
3. Controlliamo con un if se nel json c'é la stringa URL d'un altro frammento precedente;
   1. Se c'é, chiamiamo quell'url.
4. A prescindere da se cé uno no, aggiungiamo l'url dell'esecuzione corrente per la ripresa in caso questo sia il primo frammento, se peró l'url cé prima chiamiamo e poi avviene la sovrascrittura del DB con l'url dell'esecuzione corrente;
5. Ci mettiamo in attesa di chiamata da altro workflow/frammento, con timer in caso non ci sia nessun altro frammento che possa chiamarci

````
{
  "nodes": [
    {
      "parameters": {
        "conditions": {
          "options": {
            "caseSensitive": true,
            "leftValue": "",
            "typeValidation": "strict",
            "version": 3
          },
          "conditions": [
            {
              "id": "230fb49a-a6ea-4a36-9276-056b4c8dbf2e",
              "leftValue": "={{ $('Match ID and status').first().json.resumeURL_forShardedInputs }}",
              "rightValue": "",
              "operator": {
                "type": "string",
                "operation": "notEmpty",
                "singleValue": true
              }
            }
          ],
          "combinator": "and"
        },
        "options": {}
      },
      "type": "n8n-nodes-base.if",
      "typeVersion": 2.3,
      "position": [
        -11232,
        2176
      ],
      "id": "18133b97-ddda-4d98-91fb-c2e51251f09d",
      "name": "Check_resumeUrl"
    },
    {
      "parameters": {
        "operation": "update",
        "dataTableId": {
          "__rl": true,
          "value": "8Sr0I5aUg6C8zN18",
          "mode": "list",
          "cachedResultName": "Customer_Credentials",
          "cachedResultUrl": "/projects/4VxCGfA6xewekina/datatables/8Sr0I5aUg6C8zN18"
        },
        "matchType": "allConditions",
        "filters": {
          "conditions": [
            {
              "keyName": "chatID",
              "keyValue": "={{ $('Match ID and status').item.json.chatID }}"
            }
          ]
        },
        "columns": {
          "mappingMode": "defineBelow",
          "value": {
            "resumeURL_forShardedInputs": "={{ $execution.resumeUrl }}"
          },
          "matchingColumns": [],
          "schema": [
            {
              "id": "crediti",
              "displayName": "crediti",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "number",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "customer_name",
              "displayName": "customer_name",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "email_sender",
              "displayName": "email_sender",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "chatID",
              "displayName": "chatID",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "control_sheet_id",
              "displayName": "control_sheet_id",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "lang",
              "displayName": "lang",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "account_status",
              "displayName": "account_status",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "paid",
              "displayName": "paid",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "boolean",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "stripe_customer_id",
              "displayName": "stripe_customer_id",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "pro",
              "displayName": "pro",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "boolean",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "call_proposal_agent",
              "displayName": "call_proposal_agent",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "boolean",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "mail1_example",
              "displayName": "mail1_example",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "case_study",
              "displayName": "case_study",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "wa_outreach",
              "displayName": "wa_outreach",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "wa_followup",
              "displayName": "wa_followup",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "access_token",
              "displayName": "access_token",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "access_token_timestamp",
              "displayName": "access_token_timestamp",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "access_token_expires_in",
              "displayName": "access_token_expires_in",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "refresh_token",
              "displayName": "refresh_token",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "refresh_token_timestamp",
              "displayName": "refresh_token_timestamp",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "refresh_token_expires_in",
              "displayName": "refresh_token_expires_in",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "brevo_api_key",
              "displayName": "brevo_api_key",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "brevo_to_n8n_webhook",
              "displayName": "brevo_to_n8n_webhook",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "tos_and_privacy_accepted",
              "displayName": "tos_and_privacy_accepted",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "boolean",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "tos_approval_timestamp",
              "displayName": "tos_approval_timestamp",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "privacy_approval_timestamp",
              "displayName": "privacy_approval_timestamp",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "payment_link",
              "displayName": "payment_link",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "payment_link_expires_at_unix",
              "displayName": "payment_link_expires_at_unix",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "payment_link_semester",
              "displayName": "payment_link_semester",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "payment_link_semester_expires_at_unix",
              "displayName": "payment_link_semester_expires_at_unix",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "payment_link_year",
              "displayName": "payment_link_year",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "payment_link_year_expires_at_unix",
              "displayName": "payment_link_year_expires_at_unix",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "payment_link_id",
              "displayName": "payment_link_id",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "payment_link_semester_id",
              "displayName": "payment_link_semester_id",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "payment_link_year_id",
              "displayName": "payment_link_year_id",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "msg_del",
              "displayName": "msg_del",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "parent_folder_id",
              "displayName": "parent_folder_id",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "ready_for_customizing_instructions_for_emails",
              "displayName": "ready_for_customizing_instructions_for_emails",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "boolean",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "credits_spent_lifetime",
              "displayName": "credits_spent_lifetime",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "number",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "b_t_one",
              "displayName": "b_t_one",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "boolean",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "b_t_two",
              "displayName": "b_t_two",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "boolean",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "b_t_three",
              "displayName": "b_t_three",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "boolean",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "link_item_one",
              "displayName": "link_item_one",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "link_item_two",
              "displayName": "link_item_two",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "link_item_three",
              "displayName": "link_item_three",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "linkItem_One_ExpiresAtUnix",
              "displayName": "linkItem_One_ExpiresAtUnix",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "linkItem_Two_ExpiresAtUnix",
              "displayName": "linkItem_Two_ExpiresAtUnix",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "linkItem_Three_ExpiresAtUnix",
              "displayName": "linkItem_Three_ExpiresAtUnix",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "linkItem_One_Id",
              "displayName": "linkItem_One_Id",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "linkItem_Two_Id",
              "displayName": "linkItem_Two_Id",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "linkItem_Three_Id",
              "displayName": "linkItem_Three_Id",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "current_plan",
              "displayName": "current_plan",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "number",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "boss_business_type_and_industry",
              "displayName": "boss_business_type_and_industry",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "boss_business_ups",
              "displayName": "boss_business_ups",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "boss_business_products",
              "displayName": "boss_business_products",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "last_pa_message",
              "displayName": "last_pa_message",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "product_analyst_summary",
              "displayName": "product_analyst_summary",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "product_analyst_summary_step_count",
              "displayName": "product_analyst_summary_step_count",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "number",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "modify_products",
              "displayName": "modify_products",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "boolean",
              "readOnly": false,
              "removed": true
            },
            {
              "id": "resumeURL_forShardedInputs",
              "displayName": "resumeURL_forShardedInputs",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": false
            }
          ],
          "attemptToConvertTypes": false,
          "convertFieldsToString": false
        },
        "options": {}
      },
      "type": "n8n-nodes-base.dataTable",
      "typeVersion": 1.1,
      "position": [
        -10912,
        2192
      ],
      "id": "96341f1d-271f-43bf-8be0-10957598df98",
      "name": "Add_current_resumeUrl"
    },
    {
      "parameters": {
        "url": "={{ $('Match ID and status').first().json.resumeURL_forShardedInputs }}/sharded_message",
        "sendHeaders": true,
        "headerParameters": {
          "parameters": [
            {
              "name": "=sharded",
              "value": "={{ true }}"
            }
          ]
        },
        "options": {}
      },
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 4.3,
      "position": [
        -11104,
        2064
      ],
      "id": "1e60550e-c2c4-4b01-a021-f98dd15884d8",
      "name": "Call wait node",
      "onError": "continueErrorOutput"
    },
    {
      "parameters": {
        "resume": "webhook",
        "limitWaitTime": true,
        "resumeAmount": 0.6,
        "resumeUnit": "seconds",
        "options": {
          "webhookSuffix": "sharded_message"
        }
      },
      "type": "n8n-nodes-base.wait",
      "typeVersion": 1.1,
      "position": [
        -10720,
        2192
      ],
      "id": "245a4c1e-5938-4e14-ad8d-88086f6d677a",
      "name": "Wait",
      "webhookId": "012c2956-01c0-458f-bda1-47c0d4e1a79c"
    },
    {
      "parameters": {
        "mode": "expression",
        "numberOutputs": 2,
        "output": "={{\n\n// 1. Controllo se l'oggetto json è vuoto/inesistente -> Route 1 (\"no sub found\")\n$('Match ID and status').item.json === null || \n$('Match ID and status').item.json === undefined || \nObject.keys($('Match ID and status').item.json).length === 0 ? 1 : 0\n\n}}"
      },
      "type": "n8n-nodes-base.switch",
      "typeVersion": 3.4,
      "position": [
        -11360,
        2336
      ],
      "id": "474d7cbb-b0a3-4f34-bd17-58ac34a09d19",
      "name": "Switch4"
    },
    {
      "parameters": {
        "operation": "get",
        "dataTableId": {
          "__rl": true,
          "value": "8Sr0I5aUg6C8zN18",
          "mode": "list",
          "cachedResultName": "Customer_Credentials",
          "cachedResultUrl": "/projects/4VxCGfA6xewekina/datatables/8Sr0I5aUg6C8zN18"
        },
        "matchType": "allConditions",
        "filters": {
          "conditions": [
            {
              "keyName": "chatID",
              "keyValue": "={{ $('Webhook').item.json.body.message.chat.id }}"
            }
          ]
        },
        "limit": 1
      },
      "type": "n8n-nodes-base.dataTable",
      "typeVersion": 1.1,
      "position": [
        -11520,
        2336
      ],
      "id": "2d86a693-60db-4620-af61-47754ed6f361",
      "name": "Match ID and status"
    }
  ],
  "connections": {
    "Check_resumeUrl": {
      "main": [
        [
          {
            "node": "Call wait node",
            "type": "main",
            "index": 0
          }
        ],
        [
          {
            "node": "Add_current_resumeUrl",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Add_current_resumeUrl": {
      "main": [
        [
          {
            "node": "Wait",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Call wait node": {
      "main": [
        [
          {
            "node": "Add_current_resumeUrl",
            "type": "main",
            "index": 0
          }
        ],
        []
      ]
    },
    "Wait": {
      "main": [
        []
      ]
    },
    "Switch4": {
      "main": [
        [
          {
            "node": "Check_resumeUrl",
            "type": "main",
            "index": 0
          }
        ],
        []
      ]
    },
    "Match ID and status": {
      "main": [
        [
          {
            "node": "Switch4",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  },
  "pinData": {},
  "meta": {
    "templateCredsSetupCompleted": true,
    "instanceId": "877c7cb3d49866a5945e850a30461cea95b771efd663093339d70959d8c23bef"
  }
}
````


# Verdetto
Nemmeno questa volta ha funzionato come desiderato perché risultava non ci fossero URL nel DB di altri flussi da chiamare.


