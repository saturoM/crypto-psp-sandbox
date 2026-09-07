# Domain · Crypto PSP sandbox

## One-liner

Merchant creates an **invoice**. Money is not “in” until a **captured** (or equivalent settle) event. Create/click ≠ funds.

## Core entities

| Entity | Meaning |
|---|---|
| **Merchant** | Who owns callback URL + API key |
| **Invoice** | Intent to collect/pay an amount in a currency |
| **Payment attempt** | One try against a mock rail (on-ramp / off-ramp / crypto transfer) |
| **Webhook delivery** | Signed HTTP notify to merchant about status change |
| **FxQuote** | Rate + fee snapshot attached at invoice create (or refresh) |

## Status machine (v1)

```text
created → pending → captured
                 ↘ failed
                 ↘ expired
```

Rules:

- Transitions are **idempotent** by `(invoice_id, target_status, event_id)`.
- `created` = TAKE (intent recorded).
- `captured` = FILL (mock rail confirmed; bookable fact).
- `pending` = waiting on rail / user action (e.g. mock 3DS-ish confirm).
- Illegal jumps (e.g. `failed → captured`) rejected with clear error code.

## On-ramp vs off-ramp (mock)

| Flow | In | Out | Notes |
|---|---|---|---|
| **On-ramp** | Fiat (EUR mock) | Crypto | User pays fiat; invoice settles crypto credit to merchant wallet mock |
| **Off-ramp** | Crypto | Fiat | Merchant sends crypto; payout mock to IBAN/card stub |

No real money, no real KYC vendor — stubs only.

## Idempotency

- `Idempotency-Key` on `POST /invoices` and on capture/fail actions.
- Same key + same body → same resource. Same key + different body → `409`.

## Webhooks

- Signed (`HMAC-SHA256` over body + timestamp).
- Delivered **async via broker** (not in the request thread).
- Retries with backoff; dead-letter after N failures.
- Merchant endpoint ack = 2xx.

## What we explicitly do not build in v1

- Real blockchain / real PSP keys
- Full AML scoring
- Multi-tenant billing UI polish
- Gambling lobby / games (that stays in pam-wallet)
