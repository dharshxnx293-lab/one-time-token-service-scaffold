# One-Time Token Service — Starter Scaffold

Build a single-use, expiring token service backed by Redis. The service issues
cryptographically random token IDs, stores JSON payloads with an atomic TTL,
and consumes each token exactly once using Redis GETDEL.

## Setup

```bash
npm install
cp .env.example .env      # set REDIS_URL to your Redis connection
docker start redis-m4     # make sure your Redis is running
```

## Implementation

- **`token-service.js`**
  - `issueToken(payload, ttlSeconds)` generates a 128-bit random ID with
    `crypto.randomBytes(16)`, serialises the payload, and stores it using
    one atomic `SET ... EX ... NX` command.
  - `consumeToken(tokenId)` uses atomic `GETDEL` and returns either
    `{ ok: true, payload }` or `{ ok: false, reason: "invalid" }`.
- **`test.js`**
  - Issues one token and calls `consumeToken` five times concurrently with
    `Promise.all`.
  - Asserts that exactly one call succeeds and the other four are invalid.

## Run the test

```bash
npm test
```

Expected output includes:

```text
winners: 1
PASS: token was single-use.
```

The test must be run against a live Redis instance. Paste the actual output
from your run into the Pull Request description before submitting.

## Submit

Open a Pull Request containing `token-service.js`, `test.js`, and the
passing test output (`winners: 1`).
