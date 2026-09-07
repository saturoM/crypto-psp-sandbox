# Architecture · Crypto PSP sandbox

## Compose topology

```text
┌────────────┐     ┌────────────┐     ┌──────────┐
│  React UI  │────▶│  Nest API  │────▶│ Postgres │
└────────────┘     └─────┬──────┘     └──────────┘
                         │ publish
                         ▼
                   ┌──────────┐
                   │ RabbitMQ │
                   └────┬─────┘
                        │ consume
                        ▼
                   ┌──────────────┐
                   │ Workers      │
                   │ - webhooks   │
                   │ - expire job │
                   └──────────────┘
```

## Why RabbitMQ

- Clear **pub/sub + work queues** for portfolio storytelling
- Webhook dispatch must not block HTTP create/capture
- Easy to show retry / DLQ in README and tests

## Logging

- Structured JSON (pino): `request_id`, `invoice_id`, `event_id`
- One correlation id from HTTP → publish → consumer

## API sketch (v1)

| Method | Path | Purpose |
|---|---|---|
| POST | `/v1/invoices` | Create (idempotent) |
| GET | `/v1/invoices/:id` | Read |
| POST | `/v1/invoices/:id/capture` | Mock FILL |
| POST | `/v1/invoices/:id/fail` | Mock fail |
| GET | `/v1/admin/webhook-deliveries` | Desk: delivery log |

## Tests (planned)

- Unit: status transitions + idempotency matrix
- Integration: API + Postgres + RabbitMQ in compose/testcontainers
- Contract: webhook signature verify helper
