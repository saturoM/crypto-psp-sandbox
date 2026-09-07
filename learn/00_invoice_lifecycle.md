# Лекція · Життєвий цикл одного invoice

Ідемо кроками. На кожному: хто діє, що в Postgres, що в черзі.

## 1. Create (TAKE)

| Хто | Що |
|---|---|
| Desk UI | `POST /v1/invoices` (+ Idempotency-Key) |
| API | Валідує → пише invoice `created` у Postgres → (опційно) publish `InvoiceCreated` |
| Worker | Може ще нічого не робити, або «поставити таймер expire» |
| Merchant | Поки тиша (або webhook `created`, якщо так заспекали) |

У DB з’явився факт наміру. Грошей немає.

## 2. Pending (чекаємо rail)

| Хто | Що |
|---|---|
| Desk / mock | «Користувач пішов платити» / кнопка Start payment |
| API | `created → pending` у Postgres → publish `InvoiceStatusChanged` |
| Worker | За потреби: webhook `pending` мерчанту |

## 3. Captured (FILL)

| Хто | Що |
|---|---|
| Mock rail / Desk | Confirm success |
| API | `pending → captured` (ідемпотентно по event_id) → **обов’язково** publish |
| Worker | Головна робота: підписаний webhook `captured` → лог у `webhook_deliveries` |
| Postgres | Invoice = captured; delivery attempts з’являються тут же (пише worker або API helper) |

FILL уже є, навіть якщо мерчант ще не відповів на webhook.

## 4. Fail або expire (альтернативні кінці)

- **fail:** rail сказав ні → API ставить `failed` → подія → worker може повідомити мерчанта  
- **expire:** scheduler/worker бачить TTL → просить API або сам (за спекою) завершити як `expired` — у нашому sandbox краще: worker шле команду/подію, **правило переходу** лишається узгодженим з API

З `failed` / `expired` назад у `captured` у v1 **немає**.

## Карта одним рядком

```text
UI → API → Postgres (стан)
         ↘ RabbitMQ → Worker → Merchant HTTP
```

## Lock

> Кожна зміна статусу: спочатку API+DB, потім подія. Worker не замінює create/capture.
