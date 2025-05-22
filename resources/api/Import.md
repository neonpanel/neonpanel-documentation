
# Import API Documentation

## Introduction
This API allows uploading **CSV files** to import structured data into the NeonPanel platform.  
Each import type has its own required format, which must be downloaded before upload.

---

## Supported Import Types

Use one of the following values as `{type}` in the template download or upload endpoints:

- `bill`
- `fba-inbound-shipment-dates-update`
- `forecast`
- `inventory-order`
- `invoice`
- `product`
- `shipment`
- `tpl`

---

## Get Import Template

### Description
Download the CSV template for the given import type.

### Request
- **Method:** GET
- **URL:**
  ```
  https://my.neonpanel.com/api/v1/import/{type}
  ```
- **Headers:**
    - `Authorization: Bearer <access_token>`

### Response
- **Status Code:** 200 OK
- **Body:**  
  Returns a CSV file with header columns specific to the import type.

---

## Upload CSV File

### Description
Upload a `.csv` file for importing data of a specific type.  
The file must be passed in the `template` parameter.

### Request
- **Method:** POST
- **URL:**
  ```
  https://my.neonpanel.com/api/v1/import/{type}
  ```
- **Headers:**
    - `Authorization: Bearer <access_token>`
    - `Content-Type: multipart/form-data`
- **Body (form-data):**
    - `template`: *{file}* — CSV file with data

### Example using Postman
Use `form-data`:

| Key      | Value                   | Type  |
|----------|-------------------------|--------|
| template | select your CSV file    | File  |

### Response
- **Status Code:** 200 OK
- **Body:**
  ```json
  {
      "uuid": "0a4a0a5551ce4cdea78f634920a4dba6a6dfa96a"
  }
  ```

### Notes
- `uuid` is a unique identifier of the import operation.
- You can use this `uuid` to track the import status (if such endpoint is available).

---

> ⚠️ **Important:**  
> Only CSV files are accepted. Use the [Get Import Template](#get-import-template) endpoint to download the correct format before uploading.
