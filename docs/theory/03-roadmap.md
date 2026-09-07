# Roadmap · commit-sized progress

Track progress on GitHub via small commits. Suggested order:

| # | Milestone | Done when |
|---|---|---|
| 0 | Theory pack (this) | README + domain + architecture + roadmap pushed |
| 1 | Monorepo skeleton | `apps/api`, `apps/web`, `docker-compose.yml` empty-but-bootable |
| 2 | Invoice + status in Postgres | create/get + transition rules + unit tests |
| 3 | RabbitMQ publisher/consumer | event on status change lands in queue |
| 4 | Webhook worker | signed delivery + retries + delivery log |
| 5 | Desk UI | create invoice, capture/fail, see webhooks |
| 6 | FX + fees | quote on create, shown on invoice |
| 7 | On/off-ramp mocks | two flows documented + tested |
| 8 | CI | GitHub Actions: lint + test + docker build |

## Progress log

- **2026-09-07** — Milestone 0: theory docs committed.

- **2026-09-07** — Learn module 01 (invoice path) + drill quiz pushed.
- **2026-09-07** — Deep dive: async webhooks via broker (`learn/01_webhook_async.md`).
