# Лекція · Ідемпотентність (модуль 02)

## Навіщо

Мережа й клієнти **повторюють** запити: timeout → retry, double-click, webhook/rail прийшов двічі. Без ідемпотентності легко отримати **два invoice** або **два capture**.

## Два місця

### 1) Create — `Idempotency-Key`

```text
POST /v1/invoices
Header: Idempotency-Key: merch_abc_001
Body: { amount, currency, ... }
```

| Повтор | Результат |
|---|---|
| Той самий key + той самий body | Той самий invoice (200/201), не новий рядок |
| Той самий key + **інший** body | `409 Conflict` |
| Новий key | Новий invoice |

Ключ зберігаємо в Postgres поруч із invoice (або окрема таблиця keys).

### 2) Capture — ключ події

Rail / desk шле «captured» з `event_id` (напр. `capture:inv_1:evt_9`).

| Повтор того ж event_id | Результат |
|---|---|
| Перший раз | `pending → captured`, publish у чергу |
| Другий раз | 0 нових переходів, 0 других webhook-фактів (або delivery з тим же event — не новий статус) |

Це той самий дух, що `captured:dep_*` у pam-wallet.

## Що не плутати

- Ідемпотентність ≠ «можна жати capture скільки завгодно з різними event_id» — різні id можуть бути різними подіями (спека має заборонити другий FILL).
- Ідемпотентність create не замінює auth / валідацію сум.

## Lock

> Повтор того самого ключа не змінює світ двічі. Create — Idempotency-Key. Capture — event_id.
