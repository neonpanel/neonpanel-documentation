# List Catalog Items

Returns **paged collections** of catalog entities (inventory, product, customer, vendor, warehouse, service, service‑group, product‑family, brand) with powerful filtering.
This file documents both the REST and GraphQL flavours.

---

## 1. REST Endpoint

```http
GET /api/catalog/items/list
```

> **Type‑scoped alternative**
>
> ```http
> GET /api/catalog/{itemType}
> ```

### 1.1 Authentication

```http
Authorization: Bearer {access_token}
```

### 1.2 Query Parameters

| Name               | In    | Type             | Required | Description                                                                       |
| ------------------ | ----- | ---------------- | -------- | --------------------------------------------------------------------------------- |
| `itemType`         | query | enum             | no       | Restrict to one type (`inventory`, `product`, …). Omit for mixed list.            |
| `companyId`        | query | int              | no¹      | Tenant scope. Mandatory for inventory or when the token spans multiple companies. |
| `marketplace_id`   | query | int              | no       | Filter inventory items by marketplace.                                            |
| `status`           | query | enum             | no       | `ACTIVE`, `ARCHIVED`, etc.                                                        |
| `sku`              | query | string           | no       | Case‑insensitive contains search. Supports `*` wildcard.                          |
| `name`             | query | string           | no       | Case‑insensitive contains search.                                                 |
| `updatedAfter`     | query | RFC3339 datetime | no       | Return items updated strictly after this timestamp.                               |
| `includeRelations` | query | boolean          | no       | Same behaviour as in *Get Catalog Item Details*.                                  |
| `limit`            | query | int (1‑500)      | no       | Defaults **50**.                                                                  |
| `cursor`           | query | opaque string    | no       | Pagination cursor from previous page.                                             |

> ¹ Omit if token is already restricted to a single `companyId`.

### 1.3 Response

```jsonc
{
  "meta": {
    "requestId": "75b4…",
    "timestamp": "2025‑06‑23T14:02:11Z",
    "cursor": "eyJwYWdlIjoyfQ==", // null when no further pages
    "limit": 50,
    "count": 50
  },
  "data": [
    {
      "type": "inventory",
      "id": "57e6…",
      "attributes": {
        "sku": "B07XYZ",
        "name": "Blue Widget – EU",
        "brand": "Acme",
        "status": "ACTIVE"
      }
    },
    {
      "type": "customer",
      "id": "c994…",
      "attributes": {
        "name": "Retailer GmbH",
        "defaultCurrency": "EUR",
        "status": "ACTIVE"
      }
    }
    // … up to `limit` items
  ]
}
```

### 1.4 Pagination Rules

1. Omit `cursor` on the first request.
2. When `meta.cursor` is non‑null, pass it verbatim to fetch the next page.
3. If `meta.cursor` is `null`, you have reached the final page.

### 1.5 Error Codes (delta from *Get Catalog Item Details*)

| Code | Title           | Notes                     |
| ---- | --------------- | ------------------------- |
| 413  | Limit Too Large | `limit` > 500.            |
| 422  | Invalid Cursor  | Cursor malformed/expired. |

### 1.6 Examples

```http
# List active inventory SKUs updated today
GET /api/catalog/items/list?itemType=inventory&status=ACTIVE&updatedAfter=2025‑06‑23T00:00:00Z&limit=100
Authorization: Bearer …
```

```http
# Get next page using cursor
GET /api/catalog/items/list?cursor=eyJwYWdlIjoyfQ==
Authorization: Bearer …
```

---

## 2. GraphQL Wrapper

**Endpoint**: `/graphql`

### 2.1 Query Pattern

```graphql
query ListCatalogItems(
  $filter: CatalogItemFilterInput
  $pagination: PaginationInput
  $includeRelations: Boolean = false
  $locale: String = "en-US"
) {
  catalogItems(
    filter: $filter
    pagination: $pagination
    includeRelations: $includeRelations
    locale: $locale
  ) {
    pageInfo {
      cursor          # same opaque string as REST
      limit
      count           # number of items in this page
    }
    items {
      __typename      # inventory | product | …
      id
      sku             # inventory & product only
      name
      status
      ... on Inventory @include(if: $includeRelations) {
        brand
        warehouseBalances { warehouseId onHand available }
      }
    }
  }
}
```

### 2.2 Schema Snippets

```graphql
type PageInfo {
  cursor: String
  limit: Int!
  count: Int!
}

type CatalogItemsConnection {
  pageInfo: PageInfo!
  items: [CatalogItem!]!
}

input PaginationInput {
  limit: Int = 50   # 1‑500
  cursor: String
}

input CatalogItemFilterInput {
  itemType: CatalogItemType
  companyId: Int
  marketplaceId: Int
  status: CatalogItemStatus
  skuContains: String
  nameContains: String
  updatedAfter: DateTime
}
```

> 🔒 **Auth** – supply the same bearer token in the `Authorization` header.
> Pagination rules mirror the REST API.

### 2.3 Example Query

```graphql
query Example($cursor: String) {
  catalogItems(
    filter: { itemType: inventory, status: ACTIVE, updatedAfter: "2025‑06‑23T00:00:00Z" }
    pagination: { limit: 100, cursor: $cursor }
  ) {
    pageInfo { cursor count }
    items { __typename id sku name }
  }
}
```

---

## 3. Versioning & Change Log

| Date       | Version | Notes                |
| ---------- | ------- | -------------------- |
| 2025‑06‑23 | 1.0.0   | Initial publication. |
