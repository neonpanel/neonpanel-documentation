# Get Catalog Item Details

This endpoint returns detailed information about a single catalog entity of any supported type (inventory item, product, customer, vendor, warehouse, service, service‑group, product‑family, or brand).

---

## 1. Endpoint

```http
GET /api/catalog/items
```

> **Alternative path style**
>
> ```http
> GET /api/catalog/{itemType}/{id}
> ```
>
> Use whichever URI style is most consistent with your existing REST conventions. All parameters, response shapes, and error codes are identical.

---

## 2. Authentication

All requests **must** include a valid bearer token issued by the **Auth API**.

```http
Authorization: Bearer {access_token}
```

| Error | Meaning                       |
| ----- | ----------------------------- |
| 401   | Missing / expired token       |
| 403   | Token present but lacks scope |

---

## 3. Required Parameters

| Name             | In    | Type   | Required | Description                                                                                                                  |
| ---------------- | ----- | ------ | -------- | ---------------------------------------------------------------------------------------------------------------------------- |
| `itemType`       | query | enum   | **yes**  | One of:<br>`inventory`, `product`, `customer`, `vendor`, `warehouse`, `service`, `service_group`, `product_family`, `brand`. |
| `id`             | query | UUID   | *yes*¹   | Primary identifier for non‑inventory types.                                                                                  |
| `inventoryId`    | query | UUID   | *yes*²   | Primary identifier if `itemType = inventory`.                                                                                |
| `companyId`      | query | int    | *yes*³   | Company tenant; required for the composite‑key fallback when `inventoryId` absent.                                           |
| `marketplace_id` | query | int    | *yes*³   | Marketplace identifier (Amazon, Shopify, etc.).                                                                              |
| `sku`            | query | string | *yes*³   | Merchant SKU.                                                                                                                |

> ¹ **Required for**: product, customer, vendor, warehouse, service, service\_group, product\_family, brand.
>
> ² Mutually exclusive with the composite key. If `inventoryId` is supplied the composite key is ignored.
>
> ³ Required **only** when `inventoryId` is **omitted** and `itemType = inventory`.

### Optional Parameters

| Name               | In    | Type    | Default | Description                                                                           |
| ------------------ | ----- | ------- | ------- | ------------------------------------------------------------------------------------- |
| `includeRelations` | query | boolean | `false` | If `true` the response expands related entities (prices, balances, child‑SKUs, etc.). |
| `locale`           | query | string  | `en-US` | Localises description fields where available.                                         |

---

## 4. Response

```jsonc
{
  "meta": {
    "requestId": "d3f5…",
    "timestamp": "2025-06-23T13:45:12Z"
  },
  "data": {
    "type": "inventory",         // echoes itemType
    "id": "57e6…",               // canonical id
    "attributes": {               // type‑specific fields
      "sku": "B07XYZ",
      "name": "Blue Widget – EU",
      "brand": "Acme",
      "dimension": { "unit": "cm", "l": 12, "w": 8, "h": 3 },
      "weight": { "unit": "g", "value": 120 },
      "status": "ACTIVE",
      "createdAt": "2021‑02‑14T09:16:00Z",
      "updatedAt": "2025‑06‑20T21:08:54Z"
    },
    "relationships": {
      "warehouseBalances": [
        { "warehouseId": 12, "onHand": 345, "available": 322 }
      ],
      "priceTiers": [
        { "currency": "USD", "tier": "default", "price": 19.99 }
      ]
    }
  }
}
```

### Field Notes

* **`attributes`** — varies by `itemType`. Each catalog table maps one‑for‑one into this payload segment.
* **`relationships`** — present only when `includeRelations = true`.

---

## 5. Error Codes

| Code | Title                   | When It Happens                                                      |
| ---- | ----------------------- | -------------------------------------------------------------------- |
| 400  | Validation Error        | Missing required param; unknown `itemType`; conflicting identifiers. |
| 404  | Item Not Found          | No matching record for given identifiers.                            |
| 409  | Ambiguous Composite Key | Composite key resolves to > 1 inventory records.                     |
| 500  | Internal Server Error   | Unhandled exception.                                                 |

---

## 6. Examples

### 6.1 Inventory by `inventoryId`

```http
GET /api/catalog/items?itemType=inventory&inventoryId=57e66b5d‑3d1c‑45e7‑8e4f‑0ea9b8c0d043
Authorization: Bearer …
```

### 6.2 Inventory by composite key

```http
GET /api/catalog/items?itemType=inventory&companyId=331&marketplace_id=12&sku=B07XYZ
Authorization: Bearer …
```

### 6.3 Vendor by primary id

```http
GET /api/catalog/items?itemType=vendor&id=9ab2…
Authorization: Bearer …
```

---

## 7. Versioning & Change Log

| Date       | Version | Notes                |
| ---------- | ------- | -------------------- |
| 2025‑06‑23 | 1.0.0   | Initial publication. |

---

## 7. GraphQL Wrapper

NeonPanel exposes a GraphQL endpoint that maps 1‑to‑1 onto the REST resources described above.
**Endpoint**: `/graphql`

### 7.1 Single‑item query

```graphql
query GetCatalogItem($itemType: CatalogItemType!, $id: ID, $inventoryId: ID, $companyId: Int, $marketplaceId: Int, $sku: String, $includeRelations: Boolean = false, $locale: String = "en-US") {
  catalogItem(
    itemType: $itemType
    id: $id
    inventoryId: $inventoryId
    companyId: $companyId
    marketplaceId: $marketplaceId
    sku: $sku
    includeRelations: $includeRelations
    locale: $locale
  ) {
    __typename      # inventory | product | customer | …
    id
    ... on Inventory {
      sku
      name
      brand
      weight { unit value }
      dimension { unit l w h }
      status
    }
    ... on Customer {
      name
      email
      defaultCurrency
    }
    relationships @include(if: $includeRelations) {
      ...CatalogItemRelationshipsFragment
    }
  }
}

fragment CatalogItemRelationshipsFragment on CatalogItemRelationships {
  warehouseBalances {
    warehouseId
    onHand
    available
  }
  priceTiers {
    currency
    tier
    price
  }
}
```

> 🔒 **Auth** – supply the same bearer token in the `Authorization` header.

### 7.2 Batch query example

```graphql
query BatchCatalogItems($refs: [CatalogItemRefInput!]!) {
  catalogItems(refs: $refs) {
    id
    __typename
    sku
    name
  }
}
```

### 7.3 Schema snippets

```graphql
enum CatalogItemType {
  inventory
  product
  customer
  vendor
  warehouse
  service
  service_group
  product_family
  brand
}

input CatalogItemRefInput {
  itemType: CatalogItemType!
  id: ID
  inventoryId: ID
  companyId: Int
  marketplaceId: Int
  sku: String
}
```

GraphQL returns exactly the same field names and data shapes as the REST endpoint, enabling clients to choose whichever protocol best fits their use case.

---

## 8. Future Extensions

* **Batch** lookup: accept a list of identifiers to minimise round‑trips.
* **GraphQL** wrapper for selective field queries.
* **ETag / If‑None‑Match** support for efficient polling.
