# Company API Documentation

## Introduction
This API provides functionality for managing companies in the NeonPanel platform.

## Overview
This API allows get company list in the NeonPanel platform.

## Description of parameters
- **page:** (Integer) Page number. Default value is 1.
- **per_page:** (Integer) Number of companies to return. Minimum value is 10, maximum value is 60, default is 30.
---

## Company List

### Description
This endpoint is used to get a list of companies.

### Request
- **Method:** GET
- **URL:** `https://my.neonpanel.com/api/v1/companies`
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
               "uuid": "21fe6a88bf744869a60f81a19aab209c361198bf",
               "name": "My Company",
               "currency": "USD",
               "timezone": "America/Los_Angeles"
          }
       ]
  }
  ```
