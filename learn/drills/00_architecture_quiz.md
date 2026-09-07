# Drill · 00 Architecture / roles (A/B/C)

Answers: `00_architecture_answers.md` — don’t open during the test.

**1.** Хто змінює статус invoice за правилами state machine?
- A) Desk UI напряму в Postgres
- B) API
- C) Worker, бо він «важливіший»

**2.** Worker у нашій схемі — це?
- A) Частина house, яка приймає кліки з UI замість API
- B) Частина house, яка забирає події з черги і робить фон (напр. webhook назовні)
- C) Сервер мерчанта

**3.** Правильний порядок для capture (FILL)?
- A) Worker → Postgres → API
- B) API → Postgres → (подія) RabbitMQ → Worker → merchant
- C) UI → RabbitMQ → Postgres

**4.** RabbitMQ у happy path — це?
- A) Місце, куди API публікує подію; worker consume
- B) База правди замість Postgres
- C) Браузерний канал від Desk до мерчанта

**5.** FILL уже стався, webhook мерчанту ще в черзі. Що правда?
- A) Invoice ще не captured, бо мерчант не Ack
- B) Invoice уже captured у Postgres; webhook — повідомлення про факт
- C) Треба відкатити capture, доки webhook не доставлено
