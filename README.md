# Nimvora Studio

AI creative workspace for story and document translation, story recaps and narration — for Web, macOS and Windows.

> **Status: Milestone 1 (foundation + Translation Studio).** Accounts, projects, the job system, usage
> accounting and Translation Studio work end to end. Story Recap, AI Voice, file storage, billing
> payments and the desktop apps are **not built yet** — see [docs/STATUS.md](docs/STATUS.md) for the exact state.

## Repository layout

```
apps/web/        Next.js 15 (App Router, static export) — the UI for web and, later, Tauri desktop
backend/         FastAPI API, SQLAlchemy models, Alembic migrations, job worker, AI provider adapters
docs/            Architecture, status, deployment, data-retention docs
.github/         CI (lint, types, tests on SQLite + PostgreSQL, build, dependency audit)
```

## Quick start (local development)

Requirements: Node 20.9+, Python 3.12+ (verified on 3.14).

```bash
# Backend API
cd backend
python3 -m venv .venv && .venv/bin/pip install -r requirements-dev.txt
cp .env.example .env               # set NIMVORA_ENABLE_TEST_PROVIDER=true or add a provider key
.venv/bin/alembic upgrade head
.venv/bin/uvicorn app.main:app --reload --port 8000     # API docs: http://localhost:8000/docs

# Job worker (separate terminal) — processes translation jobs
cd backend && .venv/bin/python -m app.worker

# Web app (separate terminal)
npm install
npm run dev:web                    # http://localhost:3000
```

In development, verification and password-reset emails are printed to the API log
(`NIMVORA_EMAIL_BACKEND=console`). Copy the link from there.

**AI providers.** Without a provider key, translation is disabled and the UI says so. For real
translations set `NIMVORA_ANTHROPIC_API_KEY` (or `NIMVORA_OPENAI_API_KEY` + `NIMVORA_OPENAI_MODELS`).
For development without a key, `NIMVORA_ENABLE_TEST_PROVIDER=true` enables a clearly-labelled
**test provider that does no AI work** — it echoes the source with a `[TEST]` marker. It is refused
in production.

## Checks

```bash
npm run lint:web && npm run typecheck:web && npm run test:web && npm run build:web
cd backend && .venv/bin/ruff check . && .venv/bin/ruff format --check . && .venv/bin/pytest -q
```

## Documentation

- [docs/STATUS.md](docs/STATUS.md) — what works, what doesn't, acceptance criteria, next milestones
- [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) — design decisions, data model, jobs, security
- [docs/DEPLOYMENT.md](docs/DEPLOYMENT.md) — running web, API and worker in production
- [docs/DATA_RETENTION.md](docs/DATA_RETENTION.md) — what deletion removes and what is kept
