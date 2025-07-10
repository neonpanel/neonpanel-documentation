# MCP (Model Context Protocol) – FAQ & Best Practices

This FAQ explains the structure, behavior, and integration patterns for MCP — a smart middleware layer that connects LLMs to structured backend APIs like NeonPanel, QuickBooks Online, Amazon SP API, and more.

---

## 🧠 What is MCP?

**Model Context Protocol (MCP)** is a service that receives structured JSON intents from AI agents and performs the following:

- Determines the correct backend API
- Authenticates and enforces access control
- Resolves names to internal IDs
- Caches results
- Constructs and executes API requests
- Returns structured, filtered JSON responses
- Optionally includes related references (dashboards, documents, links)

---

## 🧩 Core Responsibilities

| # | Function | Description |
|--|----------|-------------|
| 1 | API Routing | Maps intent to a backend: NeonPanel, QBO, Amazon, etc. |
| 2 | Auth & Access | Enforces access levels using access_token (user_id, company_id, role) |
| 3 | Entity Resolution | Translates names → IDs via `/resolve` endpoint |
| 4 | Caching | Avoids redundant lookups and API calls |
| 5 | API Execution | Executes one or many normalized backend calls |
| 6 | JSON Response | Returns structured data + optional references |

---

## 🔄 Example Request Flow

**User asks:** _"How much does the iPhone 13 Pro Max cost?"_

1. GPT sends:
```json
{
  "intent": "get_inventory_cost",
  "product_name": "iPhone 13 Pro Max",
  "reference": true,
  "reference_limit": 2
}
```

2. MCP:
   - Resolves product name → `inventory_id`
   - Calls: `/inventory/{inventory_id}/cost`
   - Queries reference sources for context

3. Returns:
```json
{
  "result": {
    "inventory_id": "inv_123456",
    "inventory_name": "iPhone 13 Pro Max",
    "cost": 725.32,
    "currency": "USD"
  },
  "reference": [
    {
      "type": "dashboard",
      "label": "P&L Report",
      "url": "https://neonpanel.com/dashboards/123"
    },
    {
      "type": "document",
      "label": "Vendor Contract – Apple",
      "url": "https://s3.amazonaws.com/neonpanel/docs/contract-apple.pdf",
      "access_level": "confidential"
    }
  ]
}
```

---

## 📦 Reference Handling

MCP supports the following types of contextual `reference` blocks:

| Type       | Description                            | Source |
|------------|----------------------------------------|--------|
| `dashboard`| BI dashboards and analytics            | NeonPanel |
| `project`  | Business project links (e.g. POs)      | NeonPanel |
| `document` | Files from S3 + Textract + VDB         | NeonPanel |
| `qbo_doc`  | Documents from QuickBooks Online       | QBO |
| `external` | External links (e.g. ShipsGo tracking) | 3rd-party |

These are returned as a list of objects:
```json
{
  "type": "document",
  "label": "Import Invoice",
  "url": "https://s3.amazonaws.com/neonpanel/docs/inv123.pdf",
  "access_level": "internal"
}
```

---

## 🔗 Reference Endpoints

| Endpoint                   | Returns...                                |
|----------------------------|-------------------------------------------|
| `/references/dashboards`  | List of dashboards by intent or entity    |
| `/references/projects`    | Project links by entity, company, user    |
| `/references/documents`   | Matched S3/VDB documents                  |
| `/references/qbo`         | QuickBooks documents via MCP QBO plugin   |

---

## ⚙️ Optional Request Parameters

| Param           | Type     | Purpose                                      |
|------------------|----------|----------------------------------------------|
| `reference`     | boolean  | Whether to enrich response with references   |
| `reference_limit` | integer | Max number of references to include          |

---

## 🛡️ Access Control

All MCP requests validate:

- `access_token` (usually JWT)
- `user_id`, `company_id` from token
- Access level to requested data: `public`, `internal`, `confidential`, `restricted`
- Access level to referenced materials (e.g. S3 documents)

---

## 🔐 Security Rules

- All reference items must respect access_level rules
- S3 document access is governed by metadata + tags
- MCP only exposes what user has permission to view

---

## ⚡ Latency Guidelines

| Scenario                          | Target Time |
|----------------------------------|-------------|
| Cached resolve                   | 50–150 ms   |
| Direct single API call           | 100–400 ms  |
| Resolve + API call               | 300–800 ms  |
| External API (QBO, Amazon)       | 800–3000 ms |
| Reference generation (3 endpoints) | 300–800 ms total |

For async workloads or long report building, return `task_id` and allow polling.

---

## 🔧 How GPT Should Use MCP

System prompt to GPT or other LLMs:
```
Always include "reference": true when context or follow-up actions may be relevant.
You may include "reference_limit" to limit the number of links (e.g. 3).
```

GPT should avoid resolving IDs manually — let MCP handle it using the `/resolve` endpoint.

---

## 🧠 `/resolve` Endpoint Example

```http
GET /resolve?type=inventory&query=iPhone%2013%20Pro%20Max
```

Returns:
```json
{
  "type": "inventory",
  "query": "iPhone 13 Pro Max",
  "matches": [
    {
      "id": "inv_123456",
      "name": "iPhone 13 Pro Max 128GB",
      "sku": "IP13PM128",
      "company_id": "comp_001"
    }
  ]
}
```

---

## 🔄 Example `mcp.config.json`

```json
{
  "auth": {
    "token_header": "Authorization",
    "required_scopes": ["read:data"],
    "access_control": true
  },
  "cache": {
    "enabled": true,
    "ttl_seconds": 300
  },
  "api_routes": {
    "neonpanel": "https://api.neonpanel.com",
    "quickbooks": "https://api.quickbooks.com",
    "amazon_sp": "https://sellingpartnerapi-na.amazon.com"
  },
  "resolve": {
    "endpoint": "/resolve",
    "default_limit": 5
  },
  "reference_sources": {
    "dashboards": "/references/dashboards",
    "projects": "/references/projects",
    "documents": "/references/documents",
    "qbo_docs": "/references/qbo"
  }
}
```

---

## 📚 Future Ideas

- Reference ranking by intent weight / usage stats
- Embedding-based reference discovery
- Personalized references based on user behavior
- GPT-generated reference captions / summaries

---

## 📜 License

MIT License (or your project-specific license)
