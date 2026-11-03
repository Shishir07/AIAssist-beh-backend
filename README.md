# AIAssist — Backend

FastAPI backend for AIAssist, a voice-first AI personal assistant. A LangGraph agent handles natural-language requests about todos, reminders, Google Calendar, pantry, grocery lists, and meal planning.

> **Frontend repo:** [AIAssist-beh-frontend](https://github.com/Shishir07/AIAssist-beh-frontend)

**Status:** Phase 1 complete. Phase 2 (meal planning, pantry, groceries) in progress. See [ROADMAP.md](ROADMAP.md).

---

## What it does

- Accepts text or voice (via WebSocket) and routes the request through a LangGraph agent
- Agent calls purpose-built tools: todos, reminders, Google Calendar, family profiles, pantry, grocery list, meal planning
- Returns a plain-language response (text or synthesised speech via Deepgram TTS)
- Persists all state in PostgreSQL; schedules reminders with APScheduler

---

## Built with

| | Technology |
|---|---|
| Language | Python 3.11 |
| API framework | FastAPI 0.115+ |
| AI / agent | LangGraph 0.2+, LangChain Anthropic (Claude) |
| Database | PostgreSQL 16 — SQLAlchemy async + Alembic |
| Voice | Deepgram SDK (STT + TTS) |
| Calendar | Google Calendar API (OAuth 2.0) |
| Scheduler | APScheduler 3.10 |
| Containerisation | Docker |

---

## Project layout

```
app/
├── agent/          # LangGraph state machine (graph, nodes, prompts, state)
├── db/             # SQLAlchemy models, session, Alembic migrations, seed data
├── integrations/   # Google Calendar OAuth flow
├── routes/         # FastAPI routers (REST API + voice WebSocket)
├── tools/          # Agent tools: todo, reminder, calendar, family, pantry, grocery, meal, recipe
├── voice/          # Deepgram STT and TTS wrappers
├── config.py       # Pydantic settings (reads from .env)
├── main.py         # App factory and lifespan
└── scheduler.py    # APScheduler setup
```

---

## Getting started

### Prerequisites

- Python 3.11+
- Docker (for PostgreSQL)
- [Anthropic API key](https://console.anthropic.com/)
- [Deepgram API key](https://console.deepgram.com/) — for voice (optional)
- Google OAuth 2.0 credentials — for calendar (optional)

### 1. Configure

```bash
cp .env.example .env
# Fill in ANTHROPIC_API_KEY, DEEPGRAM_API_KEY, POSTGRES_PASSWORD, APP_SECRET_KEY.
# For Google Calendar: download OAuth client JSON from Google Cloud Console
# and save to credentials/google_client_secret.json  (git-ignored).
```

### 2. Start PostgreSQL

```bash
docker run -d \
  --name aisist_db \
  -e POSTGRES_DB=aisist \
  -e POSTGRES_USER=aisist \
  -e POSTGRES_PASSWORD=change_me \
  -p 5433:5432 \
  postgres:16
```

Or use the root `docker-compose.yml` from the full project.

### 3. Install and migrate

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
alembic upgrade head
```

### 4. Run

```bash
uvicorn app.main:app --reload --port 8000
```

API at `http://localhost:8000` · Swagger UI at `http://localhost:8000/docs`

---

## API overview

| Route | Purpose |
|---|---|
| `GET /health` | Liveness check |
| `GET /api/status` | Google Calendar connection status |
| `POST /api/chat` | Text chat — returns agent response |
| `WS /voice-ws` | Bidirectional voice WebSocket (STT → agent → TTS) |
| `GET /api/auth/google/login` | Start Google OAuth flow |
| `GET /api/auth/google/callback` | OAuth callback |
| `DELETE /api/auth/google/disconnect` | Unlink Google account |
| `GET /api/todos` | List todos |
| `GET /api/family` | List family members |

---

## Configuration reference

All settings are loaded from environment variables (see `.env.example`):

| Variable | Description |
|---|---|
| `ANTHROPIC_API_KEY` | Claude API key |
| `DEEPGRAM_API_KEY` | Deepgram key for STT and TTS |
| `GOOGLE_CLIENT_ID` | Google OAuth client ID |
| `GOOGLE_CLIENT_SECRET` | Google OAuth client secret |
| `DATABASE_URL` | PostgreSQL async URL (`postgresql+asyncpg://...`) |
| `APP_SECRET_KEY` | Secret for signing session tokens |
| `ENVIRONMENT` | `development` or `production` |

---

## Database migrations

```bash
# Create a new migration after changing models
alembic revision --autogenerate -m "describe the change"

# Apply all pending migrations
alembic upgrade head

# Roll back one step
alembic downgrade -1
```

---

## License

[MIT](LICENSE)
