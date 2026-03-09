# Flight App Codex

Monorepo structure:

- `backend/` -> Django REST API
- `frontend/` -> React (Vite) UI
- `docker-compose.yml` -> local fullstack stack (`backend + frontend + postgres + redis`)

## Live

- Frontend: `https://umitdev-flight-frontend.onrender.com`
- Backend: `https://umitdev-flight-backend.onrender.com`
- API docs: `/swagger/`

## Environment Files

Recommended usage:

- `.env.example` -> backend env template (reference only)
- `frontend/.env.example` -> frontend local env template
- `frontend/.env.production.example` -> frontend production env template
- `.env` -> real local backend values (do not commit)

Rule:

- Commit only `*.example` files.
- Never commit real secrets/tokens/passwords.

## Local Run (Without Docker)

Backend:

```bash
cd backend
source env311/bin/activate
python -m pip install -r requirements.txt
python manage.py migrate
python manage.py runserver
```

Frontend:

```bash
cd frontend
npm install
npm run dev
```

Local URLs:

- Backend: `http://127.0.0.1:8000`
- Frontend: `http://localhost:5173`

## Local Run (Docker)

```bash
docker compose up --build -d
docker compose ps
```

If local PostgreSQL already uses `5432`, compose uses `5433:5432`.

## Deploy (Current)

- Backend: Render Web Service (Docker, root `backend/`)
- Frontend: Render Web Service (Docker, root `frontend/`)
- DB: Neon PostgreSQL (`flight_app`)

Detailed steps:

- `DEPLOY_CHECKLIST.md`
- `SMOKE_TEST_CHECKLIST.md`
- `PROJECT_PROGRESS.md`
