# Теорія · Чому webhook через брокер (модуль 01+)

## Проблема

Merchant хоче дізнатись: invoice став `captured`. Наївний шлях:

```text
API capture handler
  → записав captured у DB
  → HTTP POST на merchant.callback_url   ← тут же, в тому ж запиті
  → повернув 200 клієнту desk/API
```

Що ламається:

1. **Merchant повільний / лежить** — ваш capture «висить» 10–30s або падає по timeout. Гроші вже «є», але UX і SLA вашого API страждають.
2. **Merchant відповів 500** — треба ретрай. У синхронному handler ретраїти ніяково: клієнт уже чекає / з’єднання закрилось.
3. **Подвійний виклик** — клієнт ретраїть `capture`. Без окремого delivery-лога легко надіслати webhook двічі *або* загубити його.
4. **Масштаб** — 1k captures/хв × повільні callbacks = thread pool API вмирає.

## Цільовий шлях

```text
API capture
  → DB: status=captured (ідемпотентно)
  → publish InvoiceStatusChanged { invoice_id, status, event_id }
  → 200 OK клієнту   ← швидко, без HTTP до мерчанта

Worker (consumer)
  → читає подію з RabbitMQ
  → будує signed payload
  → POST merchant callback
  → пише webhook_deliveries (attempt, http_status, next_retry)
  → на fail: retry / DLQ
```

**Відповідь на тест:** шле **worker після події з брокера**, не handler і не React desk.

## Хто точно не шле webhook

| Хто | Чому ні |
|---|---|
| Capture HTTP handler (синхронно) | Блокує API; погані ретраї |
| React desk у браузері | Немає секретів підпису; мерчант вам не довіряє браузеру клієнта |
| «Fire and forget» goroutine в API без черги | Процес рестартнувся — подію втратили; немає DLQ |

## Підпис (коротко)

- `timestamp` + raw body → `HMAC-SHA256(secret, …)`
- Мерчант перевіряє підпис і свіжість timestamp (anti-replay)
- Секрет лише на API/worker, не у frontend

## Зв’язок з TAKE / FILL

- Перехід у `captured` (FILL) — **факт у вашій системі**.
- Webhook — **повідомлення** про факт. Факт уже стався, навіть якщо webhook ще в черзі.
- Мерчант може опитувати `GET /invoices/:id`, якщо webhook запізнився (poll як backup).

## Lock

> Capture комітить FILL у DB і публікує подію. Доставка мерчанту — окремий асинхронний контур з ретраями.
