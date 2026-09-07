# Drill · 01 Invoice path (A/B/C)

Відповіді окремо: `01_invoice_answers.md` — не відкривай під час тесту.

**1.** Merchant зробив `POST /invoices`, UI показав «Created». Що правда?
- A) У мерчанта вже є captured crypto/fiat
- B) Це TAKE: статус `created`, FILL ще немає
- C) Webhook `captured` уже гарантовано доставлено

**2.** Коли вперше можна вважати гроші «фактом» у sandbox?
- A) Після Success у desk UI
- B) Після переходу в `pending`
- C) Після `captured` (mock rail confirm)

**3.** Хто шле webhook мерчанту в цільовій архітектурі?
- A) Синхронно в тому ж HTTP handler, що зробив capture
- B) Worker після події з брокера
- C) React desk напряму з браузера

**4.** Другий `POST /invoices` з тим самим `Idempotency-Key` і тим самим body?
- A) Другий invoice
- B) Той самий invoice (ідемпотентність)
- C) Завжди 500

**5.** `failed → captured`?
- A) Дозволено — «виправили»
- B) Заборонено в v1 status machine
- C) Лише через UI override без API
