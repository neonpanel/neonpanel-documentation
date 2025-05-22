# Warehouse API Documentation

## Introduction
This API provides functionality for managing warehouses in the NeonPanel platform.

## Description of parameters
The updated descriptions of the expected parameters now include the item list limitation and information about the company UUID: [Get Company UUID](Company.md)

- **page:** (Integer) Page number. Default value is 1.
- **per_page:** (Integer) Number of companies to return. Minimum value is 10, maximum value is 60, default is 30.
---

## Warehouse List

### Description
This API allows get warehouse list.

### Request
- **Method:** GET
- **URL:** `https://my.neonpanel.com/api/v1/companies/<company_uuid>/warehouses`
- **Headers:**
    - `Authorization: Bearer <access_token>`
- **Body:** (empty)

### Response
- **Status Code:** 200 OK
- **Body:**
  ```json
  {
      "current_page": 1,
      "per_page": 2,
      "last_page": 1,
      "data": [
          {
              "uuid": "3be62ff96d604a74a9f7e29955e713f04ade20bd",
              "amazon_marketplace_id": "ATVPDKIKX0DER",
              "country": "United States",
              "currency_iso": "USD"
          },
          {
              "uuid": "361781bbb36b45d48d35248be97980ae7345799a",
              "amazon_marketplace_id": "A1F83G8C2ARO7P",
              "country": "United Kingdom",
              "currency_iso": "GBP"
          }
      ]
  }
  ```
