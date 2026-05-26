# Authentication Guide

Bound supports three authentication methods depending on the account type:

| Method | Who uses it | How |
| --- | --- | --- |
| **BIP322** | External wallet users | Sign a message with a Bitcoin wallet |
| **Passkey (WebAuthn)** | Bound Auth accounts | Authenticate with a device passkey (Face ID, Touch ID, hardware key) |
| **SRP** | Bound Auth accounts | Authenticate with a password using the Secure Remote Password protocol |

All methods return the same JWT pair — an **access token** (10 min) and a **refresh token** (7 days) — used to authorize subsequent API calls.

---

## Using tokens

### Authorize API calls

Include the access token in the `Authorization` header for all protected endpoints:

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### Refresh when expired

Each refresh token is **single-use** — replace both tokens on every successful refresh call.

```http
POST /api/auth/refresh-token
```

**Request body:**

```json
{ "refreshToken": "eyJhbGci..." }
```

**Response:**

```json
{
  "accessToken": "eyJhbGci...",
  "refreshToken": "eyJhbGci...",
  "wallet": {}
}
```

---

## BIP322 (External Wallet)

For users connecting an external Bitcoin wallet. Bound verifies ownership of the wallet address via a BIP322 signature.

### Step 1 - Sign a message

Ask the user to sign a timestamp (e.g. current Unix ms) using their Bitcoin wallet.

### Step 2 - Authenticate

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
    "wallet": {}
  }
}
```

{% hint style="info" %}
A Trading Wallet is automatically created on first authentication if one does not already exist.
{% endhint %}

---

## Passkey (WebAuthn)

For Bound Auth accounts registered with a passkey. Uses the browser WebAuthn API (`navigator.credentials.get()`).

### Step 1 - Get a challenge

```http
GET /api/auth/webauthn/challenge
```

**Response:**

```json
{
  "challengeId": "abc123...",
  "challenge": "base64-encoded-challenge",
  "expiresAt": "2024-01-01T00:10:00.000Z"
}
```

### Step 2 - Sign and login

Pass the `challenge` to `navigator.credentials.get()`, then send the assertion to:

```http
POST /api/auth/passkey/login
```

**Request body:**

```json
{
  "credentialId": "credentialId-from-device",
  "challengeId": "abc123...",
  "webauthnAssertion": {
    "authenticatorData": "base64...",
    "clientDataJson": "base64...",
    "signature": "base64..."
  }
}
```

**Response:**

```json
{
  "accessToken": "eyJhbGci...",
  "refreshToken": "eyJhbGci...",
  "encryptedBlob": "...",
  "account": {
    "_id": "...",
    "email": "user@example.com",
    "createdAt": 1700000000000
  },
  "wallets": []
}
```

---

## SRP (Password-based)

For Bound Auth accounts registered with a password. The SRP protocol ensures the password is never sent to the server in plaintext.

### Step 1 - Init SRP session

```http
POST /api/auth/srp/init
```

**Request body:**

```json
{ "email": "user@example.com" }
```

**Response:**

```json
{
  "sessionId": "srp-session-id",
  "salt": "base64-salt",
  "serverPublic": "base64-B"
}
```

### Step 2 - Verify client proof

Using `salt` and `serverPublic` (B) from Step 1, compute the SRP values locally:

- `x = H(salt || H(email:password))`
- `A = g^a mod N` (random `a`)
- `M1 = H(H(N)⊕H(g) | H(email) | salt | A | B | K)`

Then send:

```http
POST /api/auth/srp/verify
```

**Request body:**

```json
{
  "sessionId": "srp-session-id",
  "clientPublic": "base64-A",
  "clientProof": "base64-M1"
}
```

**Response (2FA disabled):**

```json
{
  "serverProof": "base64-M2",
  "accessToken": "eyJhbGci...",
  "refreshToken": "eyJhbGci...",
  "encryptedBlob": "...",
  "account": {
    "_id": "...",
    "email": "user@example.com",
    "createdAt": 1700000000000
  },
  "wallets": []
}
```

**Response (2FA enabled):**

```json
{
  "serverProof": "base64-M2",
  "twoFactorRequired": true,
  "pendingToken": "..."
}
```

### Step 3 (if 2FA enabled) - Verify TOTP code

```http
POST /api/auth/2fa/verify
```

**Request body:**

```json
{
  "pendingToken": "...",
  "otpCode": "123456"
}
```

**Response:** Same structure as the 2FA-disabled response above.

---

## Error codes

| Code | Type | HTTP Status | Description |
| --- | --- | --- | --- |
| 4002 | Invalid Token | 401 | JWT is malformed or cannot be parsed |
| 4003 | Token Expired | 401 | JWT has passed its expiration time |
| 4004 | Token Not Found | 401 | No Authorization header provided |
| 4005 | Invalid Refresh Token | 400 | Refresh token is invalid or expired |
| 4006 | User Not Found | 401 | JWT valid but user doesn't exist |
| 4007 | Signature Verification Failed | 400 | BIP322 verification failed |
| 4008 | Wallet Creation Failed | 400 | Wallet creation failed during auth |
| 4009 | Invalid Taproot Address | 400 | Address doesn't match taproot format |

## Server-to-Server / Non-Browser Requests

By default, Bound's API is protected by strict WAF (Web Application Firewall) rules that block automated tools, scripts, and server-to-server requests (such as `curl`, Postman, or backend services), returning an HTML `403 Forbidden` error.

If you are accessing the Bound API directly from a server or terminal, you must include a dedicated API key in your request headers to bypass the WAF check.

### WAF Bypass Header

Include the following header in **every** API request:

| Header Name | Header Value | Description |
| --- | --- | --- |
| `X-API-Key` | `YOUR_ASSIGNED_API_KEY` | Provided by the Bound team |

### Example via cURL

```bash
curl -i -X POST [https://api.bound.exchange/api/auth/authenticate](https://api.bound.exchange/api/auth/authenticate) \
  -H "X-API-Key: bound_ink_..." \
  -H "Content-Type: application/json" \
  -d '{
    "message": "1759413612750",
    "signature": "AUE5z8iM+Y3M6e...==",
    "address": "bc1py27...zlzu",
    "publicKey": "0324e27cae...7c03"
  }'
