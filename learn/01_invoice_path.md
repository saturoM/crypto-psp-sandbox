# Лекція · Invoice money path (модуль 01)

Каса / UI **не** пише «баланс мерчанта». Лише API + книга (пізніше) фіксують факти.

## Карта

| Ідея | У цьому sandbox |
|---|---|
| TAKE | `POST /invoices` → статус `created` (намір зібрати/виплатити) |
| FILL | mock rail → `captured` (факт; можна «проводити») |
| Не FILL | UI Success, «оплачено в браузері», 3DS-like confirm без capture |

## Статуси v1

```text
created → pending → captured
                 ↘ failed
                 ↘ expired
```

1. **created** — invoice записаний, грошей ще немає.
2. **pending** — чекаємо rail / підтвердження (mock on-ramp step).
3. **captured** — rail підтвердив; це FILL.
4. **failed** — відмова / помилка rail; кінець спроби.
5. **expired** — TTL вийшов без capture.

## Ідемпотентність

- Ключ на create: `Idempotency-Key` → той самий invoice.
- Ключ на подію capture: `event_id` (або `capture:inv_*`) → друга подія = 0 нових переходів.

## Webhook

Статус змінився → **не** в тому ж HTTP-запиті до мерчанта. Публікуємо в брокер → worker шле підписаний POST. Інакше timeout мерчанта ламає ваш API.

## Lock на іспит

> Create invoice ≠ money. Captured = FILL. Webhook async.
