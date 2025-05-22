# Auth API Documentation

## Introduction
This API provides authentication functionality for accessing the NeonPanel platform.

## Overview
This API allows users to authenticate and obtain access tokens for accessing other endpoints within the NeonPanel platform.

## Authentication
The preferred way to authenticate with the API is by using client ID and client secret.

## Error Codes
The API may return the following error codes:
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
- **Body:**
  ```json
  {
    "token_type": "Bearer",
    "expires_in": 15897600,
    "access_token": "dgewqereXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJhdWQiOiI5YjkyMWFkYi0wZDlkLTQ4YjUtYWYxOC1mNzNjZjY5NTcwOWIiLCJqdGkiOiJlMzU2MjUwMDhkZWNlMmIxODQ2ZTA3ZTExY2Q3ZGYyZTlhNWFlMGRkMzVjZTA3YjFhNWY2YWYzMzBlZjg2Mjc3ZDc2YjQ2OTJjYTI1MzYwMiIsImlhdCI6MTcxMDkyOTU1Ny45NDQyNTUsIm5iZiI6MTcxMDkyOTU1Ny45NDQyNTcsImV4cCI6MTcyNjgyNzE1Ny45Mzk4NDcsInN1YiI6IjQ2Iiwic2NvcGVzIjpbXX0.Y7HY-2jFqDShZp4r8uTiZ-do-T2y5N5o3aDj_da3ZtBMo6LrqCceYtky48HJwgEtzN_IAGjpN3FkB2w47q0Xd0zUEB9cEyims6WBXExlkPli3F-XKjjo4b37pL9yIigdluCSc1Hd_Uc5NbNG4QqCvFf3xhrCKPMG1Tfz3eKqZ2uiIjeE4MaQAKXh5IAXT218Tk7vwXBRLB9XKZ5nehD4ZUI8vukMy8PZfRATGOCsdblJVvBEWKuwYu-5FJ66rVBZ8SiwZv2eHCFSl7J2yWkFb7J-4hmdIhWK0GSzxZyChUKl5FNgnVGE2g81up-n_vxEW-JF_dBK9zN6_pI2Ova8QClfnlbgIpvTl-qNRPw9huXraohgNuT-MRe4y8C9vFmPwCthQePB3_c9pkaUnyeHqtGrhkJLzBdlUEogrVRmGCJLk0VxheVcMMUhxEkOmFn3xzeAuh-JGrAPQugySs0AyKyWWbPd51_Jq8RGlS8vDSPOxLzObP9W802h2BnL8DbV8TBm4Ux_PTLs6l07G21u_WLEK1F3fxElJEKmRK4rTAOIzQuFw0cAqByT3moW9TKba1B0KAvW"
  }
  ```
