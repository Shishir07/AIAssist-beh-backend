# AIAssist Backend — Roadmap

## Phase 1 — Core agent (complete)

- [x] FastAPI app with LangGraph `StateGraph` agent
- [x] LangChain Anthropic (Claude) integration
- [x] Voice WebSocket (Deepgram STT → agent → Deepgram TTS)
- [x] Todo and reminder tools
- [x] Google Calendar integration (OAuth 2.0, read + create events)
- [x] Family member profiles and preferences
- [x] PostgreSQL persistence (SQLAlchemy async + Alembic)
- [x] APScheduler for reminder delivery
- [x] Ingredient and recipe seed data

## Phase 2 — Household management (in progress)

- [ ] Pantry tools: add, remove, low-stock alerts
- [ ] Grocery list: auto-suggest from pantry and meal plan
- [ ] Meal planning: weekly plan generator based on preferences
- [ ] Recipe lookup and storage
- [ ] Shopping list export endpoint

## Phase 3 — Multi-user

- [ ] JWT / session-based authentication
- [ ] Per-user data isolation (row-level scoping)
- [ ] Push notifications for reminders

## Phase 4 — Integrations

- [ ] Supermarket price comparison
- [ ] Calendar conflict detection
- [ ] Proactive suggestions ("You're low on eggs and they're in Tuesday's meal plan")
- [ ] Multi-language voice support

## Known issues

- Google Calendar token refresh needs error-recovery handling
- Voice WebSocket drops silently on network interruption — needs reconnect logic
