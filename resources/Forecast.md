# Forecast API Documentation

## Introduction
This API provides functionality for managing forecasts in the NeonPanel platform.

## Overview
This API allows users to update and create forecasts for companies in the NeonPanel platform.

## Supported params
This API endpoint is tailored for the submission and validation of market analysis data, with a specific focus on forecasts. It mandates various attributes, each governed by specific validation rules to assure data integrity and consistency. The updated descriptions of the expected parameters now include the item list limitation and information about the company UUID:

- **name:** (Required, String) A unique name identifying the analysis, limited to 255 characters.
- **version:** (Required, String) The version of the analysis document, allowing up to 255 characters.
- **periodicity:** (Required, String) The frequency of the analysis. Permissible values include "day", "month", "week", or "year".
- **unit:** (Required, String) The measurement unit for the analysis, restricted to 255 characters.
- **type:** (Required, String) The analysis type, which can either be "target" or "related".
- **parameter:** (Required, String) A specific parameter under analysis, limited to 255 characters.
- **marketplace:** (Required, String) A code representing the involved marketplace. This code must be exactly 2 characters long and match an entry from a predefined list of marketplace codes ($m).
- **sales_channel:** (Required, String) The sales channel identifier, with a maximum length of 255 characters.
- **items:** (Required, Array) A list of items to be included in the analysis. The list is capped at 100 items to ensure manageability and performance. Each item in the array must follow specific structure and validation rules:
- **items.*.sku:** (Required, String) The stock keeping unit (SKU), serving as an inventory identifier for each item. It is capped at 255 characters and must be validated as a valid SKU for the company.
- **items.*.period:** (Required, String) The date relevant to each item's data point, in "YYYY-MM-DD" format.
- **items.*.value:** (Required, String) The value associated with each item for the specified period.

Company UUID
The company UUID, such as "21fe6a88bf744869a60f81a19aab209c361XXXX", is a unique identifier for your company within the system. This UUID is required to construct the API request URL and can be found in the Admin Panel.

---

## Create Forecast

### Description
This endpoint is used to create a forecast for a specific company.

### Request
- **Method:** POST
- **URL:** `https://my.neonpanel.com/api/v1/companies/<company_uiid>/forecasts`
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
- **Body:**
  ```json
  {
    "name": "2024-02 year forecast1",
    "version": "february-001",
    "uuid": "f8705091d5d7421f8ad5c61f308c3e7ecf19f66f",
    "updated_at": "2024-03-20T10:08:27.000000Z",
    "created_at": "2024-03-20T10:08:27.000000Z"
  }
  ```


## Update Forecast

### Description
This endpoint is used to update a forecast for a specific company.

### Request
- **Method:** PUT
- **URL:** `https://my.neonpanel.com/api/v1/companies/<company_uiid>/forecasts/<forecast_uuid>`
- **Headers:**
    - `Authorization: Bearer <access_token>`
- **Body:**
  ```json
  {
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
- **Body:** 
  ```json
  {
    "name": "2024-02 year forecast1",
    "version": "february-001",
    "uuid": "f8705091d5d7421f8ad5c61f308c3e7ecf19f66f",
    "updated_at": "2024-03-20T10:08:27.000000Z",
    "created_at": "2024-03-20T10:08:27.000000Z"
  }
  ```



