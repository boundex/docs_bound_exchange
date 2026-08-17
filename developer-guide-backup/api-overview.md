# API Overview

Bound provides a B2B API suite for programmatic access to the trading infrastructure and derivatives marketplace.

## Authentication

All protected endpoints use **JWT Bearer tokens**, obtained by authenticating with a BIP322 Bitcoin signature.

```http
Authorization: Bearer <access_token>
```

**Token lifetimes:**

* Access token: **10 minutes**
* Refresh token: **7 days**

## Endpoint groups

| Group             | Description                              |
| ----------------- | ---------------------------------------- |
| `auth`            | Authentication and token management      |
| `wallets`         | Create and manage trading wallets        |
| `transactions`    | Create, sign, and broadcast transactions |
| `pools`           | Pool data and management                 |
| `tokens`          | Token listings and details               |
| `positions`       | LP position data                         |
| `histories`       | Trade history                            |
| `etch`            | Rune etching and Virtual Mint            |
| `vm-transactions` | Virtual Mint transactions                |
| `diamond-hand`    | Diamond Hands rewards                    |
| `sodax`           | SODAX crosschain transactions            |
| `activity-feed`   | Unified activity feed                    |
| `refund`          | Refund requests                          |
| `event-source`    | Server-Sent Events (SSE)                 |
| `which-first`     | Barrier Perps — see [Barrier Perps API](../developer-guide/barrier-perps-api.md) |
