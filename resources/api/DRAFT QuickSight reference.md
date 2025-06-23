# Report Link API

Returns a pre‑signed Amazon QuickSight dashboard URL that is parameterized for the requested date range and company.

## Endpoint

`GET /api/v1/reports/link`

## Description

Given a date range and company context, the service replies with a JSON payload that contains a direct link to the relevant QuickSight report. The URL already embeds the requested parameters (`startDate`, `endDate`, and `companyId`) so the client can simply redirect the user or embed the dashboard.

## Required Query Parameters

| Name           | Type                  | Description                                                                                                                  |
| -------------- | --------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| `startDate`    | `string` (YYYY‑MM‑DD) | Beginning of the reporting period (inclusive).                                                                               |
| `endDate`      | `string` (YYYY‑MM‑DD) | End of the reporting period (inclusive).                                                                                     |
| `companyId`    | `string` (UUID)       | Tenant / company identifier.                                                                                                 |
| `access_token` | `string`              | OAuth2 bearer token authorising the caller. Can be passed as a query parameter **or** in the `Authorization: Bearer` header. |

## Optional Query Parameters

| Name       | Type     | Description                                                          |
| ---------- | -------- | -------------------------------------------------------------------- |
| `reportId` | `string` | Override the default report with a specific QuickSight dashboard ID. |

## Response

`200 OK`

```json
{
  "reportName": "Sales‑vs‑Cost Overview",
  "description": "Dashboard tracking GMV, COGS and profit for the selected period.",
  "url": "https://quicksight.aws.amazon.com/sn/dashboards/abc123#p.startDate=2025-01-01&p.endDate=2025-01-31&p.companyId=42"
}
```

### Response Fields

| Field         | Type     | Description                                                  |
| ------------- | -------- | ------------------------------------------------------------ |
| `reportName`  | `string` | Human‑friendly title of the QuickSight dashboard.            |
| `description` | `string` | One‑line description of what the dashboard shows.            |
| `url`         | `string` | Fully qualified QuickSight URL including parameter bindings. |

## Error Responses

| Code                        | Meaning                 | When it happens                                              |
| --------------------------- | ----------------------- | ------------------------------------------------------------ |
| `400 Bad Request`           | Validation failed       | Missing or malformed required parameters.                    |
| `401 Unauthorized`          | No valid `access_token` | Token absent, expired or lacks scope.                        |
| `404 Not Found`             | Report unavailable      | The specified `reportId` or default dashboard was not found. |
| `500 Internal Server Error` | Unexpected exception    | Transient or server‑side failure.                            |

## Example

**Request**

```http
GET /api/v1/reports/link?startDate=2025-05-01&endDate=2025-05-31&companyId=42&access_token=eyJraWQ...
```

**Response**

```json
{
  "reportName": "May 2025 Profitability",
  "description": "Profit & Loss metrics for May 2025",
  "url": "https://quicksight.aws.amazon.com/sn/dashboards/profitability#p.startDate=2025-05-01&p.endDate=2025-05-31&p.companyId=42"
}
```