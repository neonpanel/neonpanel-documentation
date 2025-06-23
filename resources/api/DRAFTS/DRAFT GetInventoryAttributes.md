# Catalog Item Attributes (Denormalised Dimensions) API

## REPLACE in responce "...Path" with "...Url" adding 'https://my.neonpanel or https:// s3 (??) ... ' starting part !!

This endpoint delivers a **fully denormalised view** of one or more *inventory* items, combining core attributes with operational metrics, Amazon listing data, product‑family info, sales‑rank snapshots, estimated FBA fees, historical vendor order averages, brand assets, user assignments, and tag clouds.

Typical use‑cases:

* Power the AI **“Tell me everything about this SKU”** prompt.
* Feed data‑science notebooks that need a wide table in a single round‑trip.
* Export enriched item catalogues for BI tools.

> **Scope** – Only `inventory` items are supported.

---

## 1. REST Endpoint

```http
GET /api/catalog/items/dimensions
```

### 1.1 Authentication

Same bearer‑token mechanism as other catalog endpoints.

### 1.2 Query Parameters

| Name             | In    | Type             | Required | Description                                                                 |
| ---------------- | ----- | ---------------- | -------- | --------------------------------------------------------------------------- |
| `inventoryId`    | query | UUID             | yes¹     | Single item lookup. Mutually exclusive with `inventoryIds` & composite key. |
| `inventoryIds`   | query | CSV of UUIDs     | yes¹     | Up to **1000** IDs per request.                                             |
| `companyId`      | query | int              | no²      | Needed for composite‑key lookup.                                            |
| `marketplace_id` | query | int              | no²      | Composite‑key lookup.                                                       |
| `sku`            | query | string           | no²      | Composite‑key lookup (exact match).                                         |
| `limit`          | query | int (1‑500)      | no       | Used only with `inventoryIds` omitted; defaults **50**.                     |
| `cursor`         | query | opaque string    | no       | Pagination cursor.                                                          |
| `updatedAfter`   | query | RFC3339 datetime | no       | Filter by `inventory_items.updated_at`.                                     |

> ¹ Submit **exactly one** of `inventoryId`, `inventoryIds`, or the composite key.
>
> ² Composite key is only valid when looking up *one* item.

### 1.3 Response Structure

```jsonc
{
  "meta": {
    "requestId": "2fbe…",
    "timestamp": "2025‑06‑23T15:17:03Z",
    "cursor": null,
    "limit": 50,
    "count": 1
  },
  "data": [
    {
      "inventoryId": "57e66b5d‑3d1c‑45e7‑8e4f‑0ea9b8c0d043",
      "companyId": 331,
      "skuKey": "B07XYZ‑331‑12",
      "fnSku": "X00123ABC",
      "sku": "B07XYZ",
      "brand": "Acme",
      "pattern": "Striped",
      "dailyUnitSalesTarget": 12,
      "targetPrice": 19.99,
      "leadTimeDays": 35,
      "safetyStockDays": 21,
      "fbaLeadTimeDays": 12,
      "fbaSafetyStockDays": 14,
      "brandLogoPath": "/brands/acme/logo.png",
      "childAsin": "B09QWERTY",
      "parentAsin": "B09PARENT",
      "color": "Blue",
      "size": "M",
      "asinImgPath": "/asin/B09QWERTY.png",
      "title": "Acme Striped Widget – Blue / M",
      "parentTitle": "Acme Striped Widget",
      "parentAsinImgPath": "/asin/B09PARENT.png",
      "tags": "widget,blue,striped",
      "productFamily": "Summer 2025 Widgets",
      "productFamilyImgPath": "/families/2025summer.png",
      "userEmail": "buyer@acme.com",
      "userName": "Taylor Smith",
      "userAvatarPath": "/users/123.png",
      "productImgPath": "/products/B07XYZ.png",
      "vendor": "Shenzhen Widgets Co.",
      "vendorLogoPath": "/vendors/swco.png",
      "marketplaceId": 12,
      "htcCode": "950300",
      "duty": 0.04,
      "extraDuty": 0.00,
      "verified": true,
      "isActive": true,
      "amazonProductType": "TOYS_AND_GAMES",
      "salesRank": 4321,
      "amazonMainProductCategory": "Toys & Games",
      "amazonProductCategories": [
        "Toys & Games",
        "Building Toys"
      ],
      "estimatedFbaFee": 4.35,
      "estimatedReferralPercent": 0.15,
      "targetReferralPercent": 0.12,
      "targetFbaFee": 4.00,
      "averageOrderQuantity": 480,
      "averageVendorPrice": 7.25
    }
  ]
}
```

*The JSON keys mirror the column aliases in the SQL view provided.*

### 1.4 Error Codes (delta)

| Code | Title                   | Notes                                       |
| ---- | ----------------------- | ------------------------------------------- |
| 400  | Conflicting Identifiers | More than one identifier strategy supplied. |
| 413  | Too Many IDs            | `inventoryIds` list > 1000.                 |
| 500  | Query Timeout           | DB view exceeded 30 s execution window.     |

---

## 2. GraphQL Wrapper

**Endpoint**: `/graphql`

### 2.1 Query Example – single item

```graphql
query CatalogItemDimensions($inventoryId: ID!) {
  catalogItemDimensions(inventoryId: $inventoryId) {
    inventoryId
    sku
    brand
    salesRank
    estimatedFbaFee
    averageOrderQuantity
    # + the rest of the flat fields
  }
}
```

### 2.2 Query Example – paged list

```graphql
query CatalogItemsDimensions($filter: CatalogItemDimensionsFilter, $pagination: PaginationInput) {
  catalogItemsDimensions(filter: $filter, pagination: $pagination) {
    pageInfo { cursor count }
    items {
      inventoryId
      skuKey
      productFamily
      salesRank
      isActive
    }
  }
}
```

### 2.3 Schema Snippets

```graphql
type CatalogItemDimensions {
  inventoryId: ID!
  companyId: Int!
  skuKey: String!
  fnSku: String
  sku: String!
  brand: String
  pattern: String
  dailyUnitSalesTarget: Float
  targetPrice: Float
  # … (all other fields from the REST response)
}

input CatalogItemDimensionsFilter {
  inventoryIds: [ID!]
  companyId: Int
  marketplaceId: Int
  skuContains: String
  updatedAfter: DateTime
}
```

Pagination rules and auth mirror other catalog GraphQL endpoints.

---

## 3. Performance Notes & Caching

* The underlying SQL view (`vw_inventory_dimensions`) refreshes in **near‑real‑time** (< 5 min delay) via materialised‐view refresh.
* The endpoint supports `Cache‑Control: max‑age=60` by default; override with `?noCache=true`.
* Large requests (`inventoryIds` list) may be processed asynchronously in future versions (client receives `202 Accepted` with polling URL).

---

## 4. Change Log

| Date       | Version | Notes                                   |
| ---------- | ------- | --------------------------------------- |
| 2025‑06‑23 | 1.0.0   | Initial draft based on denormalised SQL |
