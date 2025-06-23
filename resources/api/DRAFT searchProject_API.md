# `GET /api/projects/search`

## Description

Search for projects by project name, reference number, vendor, data scope, or SKU, filtered by company and optional date range.

Returns a list of matching projects with key details including project name, reference number, vendor, customer, warehouse, marketplace, total amount, and project URL.

---

## Authentication

This endpoint **requires authentication**.

Include a valid access token in the `Authorization` header of the request:

```
Authorization: Bearer <access_token>
```

If the token is missing or invalid, the API will return a `401 Unauthorized` error.

---

## Request

### Method

`GET`

### Query Parameters

| Parameter   | Type    | Required | Description                                                                                |
| ----------- | ------- | -------- | ------------------------------------------------------------------------------------------ |
| `companyId` | string  | Yes      | The ID of the company requesting the search.                                               |
| `q`         | string  | Yes      | Search string to match against project name, reference number, vendor, data scope, or SKU. |
| `startDate` | string  | No       | Start date in `YYYY-MM-DD` format to filter projects by creation date.                     |
| `endDate`   | string  | No       | End date in `YYYY-MM-DD` format to filter projects by creation date.                       |
| `limit`     | integer | No       | Maximum number of results to return. Default is 20.                                        |
| `offset`    | integer | No       | Offset for pagination. Default is 0.                                                       |

---

## Response

### Status Codes

* `200 OK` – Request successful, returns a list of matching projects.
* `400 Bad Request` – Missing or invalid query parameters.
* `401 Unauthorized` – Access token is missing or invalid.
* `500 Internal Server Error` – Unexpected error occurred on the server.

### Response Format

```json
{
  "results": [
    {
      "date": "2025-06-22",
      "name": "Q3 Amazon Launch",
      "ref_number": "PRJ-1209",
      "vendor": "Shanghai Imports Ltd.",
      "customer": "Acme Brands",
      "warehouse": "WH-EU-01",
      "marketplace": "Amazon UK",
      "total_amount": 15720.00,
      "project_url": "https://example.com/projects/PRJ-1209"
    },
    {
      "date": "2025-06-18",
      "name": "Reorder Spring 2025",
      "ref_number": "PRJ-1183",
      "vendor": "Global Sourcing Co.",
      "customer": "Fresh Goods LLC",
      "warehouse": "WH-US-03",
      "marketplace": "Amazon US",
      "total_amount": 23200.50,
      "project_url": "https://example.com/projects/PRJ-1183"
    }
  ],
  "total": 2
}
```

---

## Examples

### Request Example

```http
GET /api/projects/search?q=amazon+spring&companyId=abc123&startDate=2025-01-01&endDate=2025-06-30 HTTP/1.1
Host: api.example.com
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6...
```

### Response Example

```json
{
  "results": [
    {
      "date": "2025-06-22",
      "name": "Q3 Amazon Launch",
      "ref_number": "PRJ-1209",
      "vendor": "Shanghai Imports Ltd.",
      "customer": "Acme Brands",
      "warehouse": "WH-EU-01",
      "marketplace": "Amazon UK",
      "total_amount": 15720.00,
      "project_url": "https://example.com/projects/PRJ-1209"
    }
  ],
  "total": 1
}
```
