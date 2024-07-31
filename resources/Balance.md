# Inventory Balance API Documentation

## Introduction
This API provides functionality for managing inventory balances in the NeonPanel platform. It specifically handles Inventory Balances information from third-party sources, which is used for the Inventory Audit process. During this process, NeonPanel compares internally calculated data with external data to ensure accuracy and consistency.

---

## Create

### Description
This endpoint is used to create a balance list for a specific company.

### Description of parameters
The updated descriptions of the expected parameters now include the item list limitation and information about the company UUID: [Get Company UUID](Company.md)

- **items**: (Required, Array) A list of items, each containing the following properties:
- **item.*.sku**: (Required, String) A unique Stock Keeping Unit (SKU) identifier for the item, limited to 255 characters.
- **item.*.date**: (Required, String) The date when the balance was recorded, formatted as YYYY-MM-DD.
- **item.*.country_code**: (Required, String) A code representing the country in ISO 3166-1 alpha-2 format.
- **item.*.warehouse_uuid**: (Required, String) A unique identifier for the warehouse, formatted as a [UUID](Warehouse.md).
- **item.*.balance_quantity**: (Required, String) The quantity of the item available in the warehouse, represented as a string.

### Request
- **Method:** POST
- **URL:** `https://my.neonpanel.com/api/v1/companies/<company_uiid>/balances`
- **Headers:**
    - `Authorization: Bearer <access_token>`
- **Body:**
  ```json
  {
    "items": [
      {
        "sku": "SKU12345",
        "date": "2024-02-01",
        "country_code": "US",
        "warehouse_uuid": "d818a2b8e36f4ec0a6ff99b32b3361cc5d3102f6",
        "balance_quantity": "1"
      }
    ]
  }
  ```


### Response
- **Status Code:** 200 OK
- **Body:**
  ```json
  [
    {
      "uuid": "1faa753f87804bc196d184ea477e8a847ba897b6",
      "date": "2024-02-01",
      "warehouse_uuid": "d818a2b8e36f4ec0a6ff99b32b3361cc5d3102f6",
      "country_code": "US",
      "sku": "SKU12345",
      "balance_quantity": "1"
    }
  ]
  ```

---
