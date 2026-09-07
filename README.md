# crypto-psp-sandbox

Portfolio project: a **mock crypto Payment Service Provider** (invoices, status machine, webhooks, FX/fees, on/off-ramp mocks).

> Status: **theory / design first** — implementation follows in later commits. Each meaningful step is a commit so progress is visible on GitHub.

## Why this exists

Showcase Full Stack + payments thinking (PSP, idempotency, async webhooks, broker) without proprietary Kyrrex code. Complements [`pam-wallet`](https://github.com/saturoM/pam-wallet) (PAM / iGaming learn desk).

## Planned stack

| Layer | Choice |
|---|---|
| API | NestJS (TypeScript) |
| Desk UI | React + Vite |
| DB | Postgres |
| Broker | RabbitMQ (webhook dispatch + domain events) |
| Ops | Docker Compose, structured JSON logs, tests, GitHub Actions CI |

## Learn (study-style)

0. [00 Architecture overview](learn/00_architecture_overview.md) · [lifecycle](learn/00_invoice_lifecycle.md) · [FAQ](learn/00_faq_architecture.md) · [drill](learn/drills/00_architecture_quiz.md)
1. [01 Invoice path](learn/01_invoice_path.md) · [terms](learn/01_terms.md) · [drill](learn/drills/01_invoice_quiz.md)

## Docs (start here)

1. [Domain model](docs/theory/01-domain.md) — invoice, statuses, TAKE ≠ FILL
2. [Architecture](docs/theory/02-architecture.md) — services, broker, compose
3. [Roadmap](docs/theory/03-roadmap.md) — commit-sized milestones
4. [ADRs](docs/adr/) — key decisions

## Run (later)

```bash
docker compose up
```

Not wired yet — see roadmap.

## License

MIT
