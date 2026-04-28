# Authentication Guide

Bound uses **BIP322 signature verification** combined with **JWT tokens** for secure, stateless authentication.

## How it works

1. Sign a message with your Bitcoin wallet using BIP322
2. Send the signature to the authenticate endpoint
3. Receive an access token (10 min) and refresh token (7 days)
4. Include the access token in all protected API calls
5. Refresh when the access token expires

## Step 1 - Authenticate

```http
POST /api/auth/authenticate
```

**Request body:**

```json
{
  "message": "1759413612750",
  "signature": "AUE5z8iM+Y3M6e...==",
  "address": "bc1py27...zlzu",
  "publicKey": "0324e27cae...7c03"
}
```

**Response:**

```json
{
  "data": {
    "accessToken": "eyJhbGci...",
    "refreshToken": "eyJhbGci...",
    "tradingAddress": "bc1p...",
    "wallet": { ... }
  }
}
```

A Trading Wallet is automatically created on first authentication if one doesn't exist.

## Step 2 - Make protected API calls

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

## Step 3 - Refresh when expired

```http
POST /api/auth/refresh-token

{ "refreshToken": "eyJhbGci..." }
```

## Error codes

| Code | Type                          | HTTP Status | Description                          |
| ---- | ----------------------------- | ----------- | ------------------------------------ |
| 4002 | Invalid Token                 | 401         | JWT is malformed or cannot be parsed |
| 4003 | Token Expired                 | 401         | JWT has passed its expiration time   |
| 4004 | Token Not Found               | 401         | No Authorization header provided     |
| 4005 | Invalid Refresh Token         | 400         | Refresh token is invalid or expired  |
| 4006 | User Not Found                | 401         | JWT valid but user doesn't exist     |
| 4007 | Signature Verification Failed | 400         | BIP322 verification failed           |
| 4008 | Wallet Creation Failed        | 400         | Wallet creation failed during auth   |
| 4009 | Invalid Taproot Address       | 400         | Address doesn't match taproot format |

{% hint style="info" %}
**VERIFY** - Bound passkey-specific SDK and sample code to be provided by engineering team.
{% endhint %}
