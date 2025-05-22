# Marketplace API Documentation

## Introduction
This API provides functionality for managing marketplaces in the NeonPanel platform.

## Description of parameters
- **page:** (Integer) Page number. Default value is 1.
- **per_page:** (Integer) Number of companies to return. Minimum value is 10, maximum value is 60, default is 30.
---

## Marketplace List

### Description
This API allows get marketplace list.

### Request
- **Method:** GET
- **URL:** `https://my.neonpanel.com/api/v1/marketplaces`
- **Headers:**
    - `Authorization: Bearer <access_token>`
- **Body:** (empty)

### Response
- **Status Code:** 200 OK
- **Body:**
  ```json
  {
      "current_page": 1,
      "per_page": 30,
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
