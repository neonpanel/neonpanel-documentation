# 🤖 NeonPanel Assistant — GPT Integration

This guide explains how to connect your NeonPanel API to a Custom GPT using OAuth2 and a unified OpenAPI action.

---

## 🛠 Step 1: Create the Custom GPT

1. Go to [https://chat.openai.com/gpts](https://chat.openai.com/gpts)
2. Click **Create**
3. In the **Configure** tab, set:

```
Name: NeonPanel Assistant  
Description: Helps analyze orders, inventory, and finances via Shopify, Amazon, QBO
```

---

## 🔗 Step 2: Add a Unified Action (OpenAPI Schema)

Use this OpenAPI schema when adding a single action in the GPT:

```yaml
openapi: 3.1.0
info:
  title: NeonPanel GPT API
  version: 1.0.0
servers:
  - url: https://my.neonpanel.com
components:
  schemas: {}
  securitySchemes:
    OAuth2:
      type: oauth2
      flows:
        authorizationCode:
          authorizationUrl: https://my.neonpanel.com/gpt-login
          tokenUrl: https://my.neonpanel.com/auth/gpt/token
          scopes: {}
paths:
  /api/v1/ai/dispatcher:
    post:
      operationId: callDispatcher
      summary: Call dispatcher using Laravel Passport OAuth
      security:
        - OAuth2: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required:
                - provider
                - service
                - action
              properties:
                provider:
                  type: string
                  enum: [neon-panel, amazon-sp-api, quick-books]
                  example: neon-panel
                resource:
                  type: string
                  enum: [warehouses, inventory-items, projects]
                  example: companies
                action:
                  type: string
                  enum: [list]
                  example: list
                company_name:
                  type: string
                  example: MyCompany
                product_name:
                  type: string
                warehouse_name:
                  type: string
                  example: Lost & Found
                created_at_min:
                  type: string
                  example: 2025-03-01
                created_at_max:
                  type: string
                  example: 2025-03-10
                sku:
                  type: string
                asin:
                  type: string
                inventory_id:
                  type: integer
      responses:
        "200":
          description: Dispatcher result
          content:
            application/json:
              schema:
                type: object
                properties:
                  company:
                    type: object
                    properties:
                      name:
                        type: string
                        example: Florida Deals
                      uuid:
                        type: string
                        format: uuid
                        example: 28167b36a84445de8e8e89bf3288db4a57a92850
                  warehouses:
                    type: array
                    items:
                      type: object
                      properties:
                        uuid:
                          type: string
                          format: uuid
                          example: 2355d17688f24f31817421364eee6d06dd7ab6e
                        name:
                          type: string
                          example: Lost & Found
                        marketplace_uuid:
                          type: string
                          nullable: true
                          example: 3be62ff96d604a74a9f7e29955e713f04ade20bd
```

---

## 🔐 Step 3: Configure OAuth Authentication

In the **Actions > Authentication** tab for your GPT:

| Field                | Value                                                          |
|---------------------|----------------------------------------------------------------|
| Authentication Type | `OAuth`                                                       |
| Client ID           | _your NeonPanel client ID_                                     |
| Client Secret       | _your NeonPanel client secret_                                 |
| Authorization URL   | `https://my.neonpanel.com/gpt-login`                          |
| Token URL           | `https://my.neonpanel.com/auth/gpt/token`                     |
| Scope               | _leave empty_                                                  |
| Token Exchange      | `Default (POST request)`                                       |

> ⚠️ Make sure `https://chat.openai.com` is added as an allowed redirect URL on your NeonPanel OAuth client configuration.

---

## ✅ Done!

Now your GPT can securely talk to your NeonPanel backend via the dispatcher API and OAuth2 token exchange.
