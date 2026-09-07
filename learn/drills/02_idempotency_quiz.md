# Drill · 02 Idempotency (A/B/C)

Answers: `02_idempotency_answers.md`

**1.** Другий POST /invoices з тим самим Idempotency-Key і тим самим body?
- A) Другий invoice
- B) Той самий invoice
- C) Завжди 500

**2.** Той самий key, але amount у body інший?
- A) Оновити суму першого invoice
- B) `409 Conflict`
- C) Тихо створити другий

**3.** Capture прийшов двічі з тим самим event_id?
- A) Два переходи в captured і два «статусні» факти
- B) Другий раз без нового переходу
- C) Автоматичний fail invoice

**4.** Навіщо ідемпотентність у PSP?
- A) Щоб UI був гарніший
- B) Бо retry/timeout/подвійний webhook інакше дублюють гроші/факти
- C) Щоб не потрібен був Postgres
