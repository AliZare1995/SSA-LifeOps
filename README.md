# LifeOps — Capture & Review (n8n Automation)

LifeOps is an automation-first “second brain” workflow that captures inputs (Telegram + Email), normalizes them into a single item schema, stores them in a backend API, and sends daily/weekly digests back to the user. [file:311]
It is designed for production use in n8n Cloud with explicit webhook responses, validation guards, and error notification paths. [file:311][web:59][web:61]

## What it delivers (customer value)
- Fast capture: users forward/send a message and it is safely recorded as a structured item. [file:311]
- Reduced cognitive load: everything is unified into one internal format, regardless of source (Telegram/Email). [file:311]
- Habit-building output: daily and weekly digests are automatically delivered to Telegram. [file:311]
- Operational safety: validation + logging + admin notifications reduce silent failures in production. [file:311]

## Core components (high level)
### 1) LifeOps workflow (main product)
**Inbound**
- Telegram inbound webhook: `telegram/lifeops_inbound_placeholder`. [file:311]
- Email inbound webhook: `email/lifeops_inbound_placeholder`. [file:311]
- Immediate webhook acknowledgements via “Respond to Webhook” nodes (responseMode=responseNode pattern). [file:311][web:61][web:59]

**Normalize + classify**
- Normalizes input into a consistent item schema: `source, external_id, content, author, created_at, raw`. [file:311]
- Rule-based classification + optional AI classification via `config.AI_CLASSIFIER_API_URL`. [file:311]

**Store + dedupe**
- Checks existing items through `GET /items` with `source` and `external_id`. [file:311]
- Creates items via `POST /items`. [file:311]
- Updates existing items via `PUT /items/{existing_id}`. [file:311]

**Digests**
- Daily digest fetch uses `since_hours=24` and sends a Telegram message. [file:311]
- Weekly digest fetch uses `since_hours=168` and sends a Telegram message. [file:311]

**Errors**
- Central error payload builder + error logging endpoint + Telegram admin notification. [file:311]

### 2) SecondBrain API (backend/receiver)
LifeOps expects a backend base URL (`config.SECOND_BRAIN_API_BASE_URL`) that exposes an `/items` API with GET (filtering), POST (create), and PUT (update). [file:311]

## Configuration (what you must fill)
In the “Config -- Constants” node, fill the placeholders:
- `config.TELEGRAM_BOT_TOKEN` [file:311]
- `config.TELEGRAM_CHAT_ID_USER` [file:311]
- `config.TELEGRAM_CHAT_ID_ADMIN` [file:311]
- `config.SECOND_BRAIN_API_BASE_URL` [file:311]
- `config.LOGGING_ENDPOINT_URL` [file:311]
- `config.AI_CLASSIFIER_API_URL` [file:311]
- `config.AI_SUMMARIZER_API_URL` [file:311]
- `config.SSA_TRANSFORMER_API_URL` (optional) [file:311]

## Quick start (demo-ready)
1) Import the LifeOps workflow JSON into n8n Cloud and keep node names/paths unchanged. [file:311]
2) Fill “Config -- Constants”. [file:311]
3) Activate the workflow and use the Production webhook URL (n8n Cloud requires an active workflow for production webhooks). [web:59]
4) Send a Telegram message → verify it is stored (via your backend API) → run Daily/Weekly triggers to verify digest delivery. [file:311]

## Notes for reviewers
This project demonstrates production-minded workflow engineering: consistent data contracts, safe webhooks, validation guards, and digest-style user-facing outputs built on top of an API-backed storage layer. [file:311]
