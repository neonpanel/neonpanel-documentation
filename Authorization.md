# Auth API Documentation

## Introduction
This API provides authentication functionality for accessing the NeonPanel platform.

## Overview
This API allows users to authenticate and obtain access tokens for accessing other endpoints within the NeonPanel platform.

## Authentication
The preferred way to authenticate with the API is by using client ID and client secret.

## Error Codes
The API may return the following error codes:

- 401 Unauthorized: Invalid client credentials.
- 500 Internal Server Error: Server error occurred.

## Rate Limit
There is currently no rate limit enforced on the number of requests a user can send.

---

## Login

### Description
This endpoint is used to obtain an access token by providing client ID and client secret.

### Request
- **Method:** POST
- **URL:** `https://my.neonpanel.com/auth/token`
- **Body:**
    - `client_id`: The client ID provided by the NeonPanel platform.
    - `client_secret`: The client secret provided by the NeonPanel platform.

### Response
- **Status Code:** 200 OK
- **Body:** (empty)
