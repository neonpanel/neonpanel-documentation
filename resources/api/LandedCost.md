# Landed Cost API Documentation

## Introduction

This API provides functionality for retrieving landed costs from the NeonPanel platform.

---

## Landed Costs List by Inventory

### Description

This endpoint is used to get a list of inventory's landed costs.

### Description of parameters

- **page:** (Optional, Integer) Page number. Default value is 1.
- **per_page:** (Optional, Integer) Number of items to return. Minimum value is 10, maximum value is 60, default is 30.
- **country_code**: (Required, String) A ISO 3166-1 alpha-2 code of country.
- **start_date**: (Required, String) The date to begin list from, formatted as YYYY-MM-DD.
- **end_date**: (Optional, String) The date to end list to, formatted as YYYY-MM-DD. Default is today's date.

### Request

- **Method:** GET
- **URL:** `https://my.neonpanel.com/api/v1/companies/<company_uuid>/inventories/{inventory_id}/landed-costs`
- **Headers:**
    - `Authorization: Bearer <access_token>`
- **Body:**

```json
{
  "page": 1,
  "per_page": 30,
  "country_code": "US",
  "start_date": "2025-05-01",
  "end_date": "2025-06-01"
}
```

### Response

- **Status Code:** 200 OK
- **Body:**

```json
{
  "current_page": 1,
  "per_page": 30,
  "last_page": 1,
  "company": {
    "uuid": "21fe6a88bf744869a60f81a19aab209c361198bf",
    "name": "My Company",
    "currency": "USD",
    "timezone": "America/Los_Angeles"
  },
  "inventory": {
    "id": 1234,
    "country_code": "US",
    "sku": "SKU12345",
    "fnsku": "FNSKU123",
    "asin": "ASIN134",
    "name": "Blue Socks 2ps pack"
  },
  "total_results": 1,
  "currency": "USD",
  "amount": 1492.00,
  "quantity": 100,
  "data": [
    {
      "batch": {
        "type": "InventoryOrder",
        "ref_number": "IO-000-001",
        "date": "2025-04-22",
        "status": "Completed",
        "completed": "2025-04-29 18:00:03",
        "link": "https://my.neonpanel.com/app/processes/385f7029-2b00-4703-bd09-e2ac1f339f5f/tasks/4dd6b3e4-a289-4249-bb32-f6da55417a26"
      },
      "currency": "USD",
      "amount": 1492.00,
      "quantity": 100,
      "details": [
        {
          "type": "Purchase Price",
          "document": {
            "type": "InventoryOrder",
            "ref_number": "IO-000-001",
            "date": "2025-04-22",
            "status": "Completed",
            "completed": "2025-04-29 18:00:03",
            "link": "https://my.neonpanel.com/app/processes/385f7029-2b00-4703-bd09-e2ac1f339f5f/tasks/4dd6b3e4-a289-4249-bb32-f6da55417a26"
          },
          "currency": "USD",
          "amount": 1200.00,
          "quantity": 100
        },
        {
          "type": "Freight Cost by Volume",
          "document": {
            "type": "Bill",
            "ref_number": "BILL123",
            "date": "2025-05-02",
            "status": "Paid",
            "completed": "2025-05-04 15:30:00",
            "link": "https://my.neonpanel.com/app/processes/c8d9fca4-97ea-4d85-a82d-09553a5c0853/tasks/cf909cf5-f99f-4598-aa7b-18bb4e02c250"
          },
          "currency": "USD",
          "amount": 292.00,
          "quantity": 100
        }
      ]
    }
  ]
}
```
