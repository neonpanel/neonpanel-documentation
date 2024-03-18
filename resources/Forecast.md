# Forecast API Documentation

## Introduction
This API provides functionality for managing forecasts in the NeonPanel platform.

## Overview
This API allows users to update and create forecasts for companies in the NeonPanel platform.

---

## Create Forecast

### Description
This endpoint is used to create a forecast for a specific company.

### Request
- **Method:** POST
- **URL:** `https://my.neonpanel.com/api/v1/companies/<companyUiid>/forecasts`
- **Headers:**
    - `Authorization: Bearer <access_token>`
- **Body:**
  ```json
  {
    "name": "2024-02 year forecast",
    "version": "february-001",
    "periodicity": "day",
    "unit": "USD",
    "type": "target",
    "parameter": "sales",
    "marketplace": "US",
    "sales_channel": "Online",
    "items": [
      {
        "sku": "SKU12345",
        "period": "2024-02-01",
        "value": "234"
      },
      {
        "sku": "SKU67890",
        "period": "2024-05-02",
        "value": "234"
      }
    ]
  }
  ```

### Response
- **Status Code:** 200 OK
- **Body:** (empty)


## Update Forecast

### Description
This endpoint is used to update a forecast for a specific company.

### Request
- **Method:** PUT
- **URL:** `https://my.neonpanel.com/api/v1/companies/<companyUiid>/forecasts`
- **Headers:**
    - `Authorization: Bearer <access_token>`
- **Body:**
  ```json
  {
    "name": "2024-02 year forecast",
    "version": "february-001",
    "periodicity": "day",
    "unit": "USD",
    "type": "target",
    "parameter": "sales",
    "marketplace": "US",
    "sales_channel": "Online",
    "items": [
      {
        "sku": "SKU12345",
        "period": "2024-02-01",
        "value": "234"
      },
      {
        "sku": "SKU67890",
        "period": "2024-05-02",
        "value": "234"
      }
    ]
  }
  ```

### Response
- **Status Code:** 200 OK
- **Body:** (empty)



