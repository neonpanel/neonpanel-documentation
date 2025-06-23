# MCP Extension – Catalog Endpoints

This document describes **how the MCP server exposes the NeonPanel catalog APIs** so that AI agents can retrieve item details or list catalog objects through a single, tool‑like interface.

> **Prerequisites** – The Auth API issues a bearer `access_token` that the MCP server must inject into every downstream call. See *Auth API Documentation* for token scopes.

---

## 1. Actions exposed to AI

| Action (`action`)  | REST Method & Path            | Purpose                                          | Required input fields                                           |
| ------------------ | ----------------------------- | ------------------------------------------------ | --------------------------------------------------------------- |
| `getCatalogItem`   | `GET /api/catalog/items`      | Fetch a single catalog entity by primary key(s). | `itemType` **AND** (`id` *or* `inventoryId` *or* composite key) |
| `listCatalogItems` | `GET /api/catalog/items/list` | Return a paged list filtered by parameters.      | none (defaults to tenant‑wide list)                             |

All other querystring parameters defined in the underlying API (**companyId**, **marketplace\_id**, **status**, *etc.*) must be passed through verbatim when supplied by the agent.

---

## 2. MCP Request & Response Shape

### 2.1 Request (from AI ➜ MCP)

```jsonc
{
  "action": "getCatalogItem",
  "params": {
    "itemType": "inventory",
    "inventoryId": "57e66b5d‑3d1c‑45e7‑8e4f‑0ea9b8c0d043",
    "includeRelations": true,
    "locale": "en‑US"
  },
  "access_token": "eyJ…"  // forwarded untouched by MCP
}
```

### 2.2 MCP ➜ REST call (injected by server)

```http
GET /api/catalog/items?itemType=inventory&inventoryId=57e66b5d-3d1c-45e7-8e4f-0ea9b8c0d043&includeRelations=true&locale=en-US
Authorization: Bearer eyJ…
```

### 2.3 Normalised response (MCP ➜ AI)

```jsonc
{
  "ok": true,
  "action": "getCatalogItem",
  "data": { /* exact JSON body returned by NeonPanel */ },
  "meta": {
    "requestId": "d3f5…",
    "source": "neonpanel.catalog"
  }
}
```

*If the REST API returns a non‑2xx status the MCP sets `ok: false` and includes `error.code` & `error.message` fields.*

---

## 3. `mcp.config.json` Snippet

Insert (or merge) the following objects into the **`endpoints`** array of your global `mcp.config.json`.

```jsonc
{
  "name": "getCatalogItem",
  "method": "GET",
  "path": "/api/catalog/items",
  "queryParams": [
    "itemType",
    "id",
    "inventoryId",
    "companyId",
    "marketplace_id",
    "sku",
    "includeRelations",
    "locale"
  ],
  "auth": {
    "type": "bearer",
    "header": "Authorization",
    "valuePrefix": "Bearer "
  }
},
{
  "name": "listCatalogItems",
  "method": "GET",
  "path": "/api/catalog/items/list",
  "queryParams": [
    "itemType",
    "companyId",
    "marketplace_id",
    "status",
    "sku",
    "name",
    "updatedAfter",
    "includeRelations",
    "limit",
    "cursor"
  ],
  "auth": {
    "type": "bearer",
    "header": "Authorization",
    "valuePrefix": "Bearer "
  }
}
```

> ✅ **Tip** – If you already expose other NeonPanel endpoints, simply append these two objects and redeploy the MCP server.

---

## 4. Prompt update (for LLM tools aware of MCP)

Add the following sentence to your system prompt:

> *“When you need catalog data, call the MCP action `getCatalogItem` or `listCatalogItems` rather than constructing raw URLs.”*

This ensures the LLM delegates API details to MCP instead of hard‑coding endpoints.

---

## 5. Validation rules enforced by MCP

1. **`itemType` presence** – Reject if missing or invalid enum value.
2. **Identifier logic** –

   * If `itemType ≠ inventory` ➜ require `id`.
   * If `itemType = inventory` ➜ require **either** `inventoryId` **or** the composite key (`companyId` + `marketplace_id` + `sku`).
3. **Composite‑key ambiguity** – If multiple records match, return error `409 AmbiguousCompositeKey` as surfaced by NeonPanel.
4. **Pagination** – For `listCatalogItems`, if `limit` > 500 MCP rewrites it to 500.

---

## 6. Example Usage Scenarios

### 6.1 LLM wants the brand name for a specific SKU

```jsonc
{
  "action": "getCatalogItem",
  "params": {
    "itemType": "inventory",
    "companyId": 331,
    "marketplace_id": 12,
    "sku": "B07XYZ"
  },
  "access_token": "…"
}
```

### 6.2 LLM needs a list of active brands

```jsonc
{
  "action": "listCatalogItems",
  "params": {
    "itemType": "brand",
    "status": "ACTIVE",
    "limit": 200
  },
  "access_token": "…"
}
```

---

## 7. Change Log

| Date       | Version | Notes                                   |
| ---------- | ------- | --------------------------------------- |
| 2025‑06‑23 | 1.0.0   | First publication – added catalog calls |
