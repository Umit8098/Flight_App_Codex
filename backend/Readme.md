# Flight Reservation API (Django REST Framework)

Production-ready flight reservation backend with token authentication, role-based permissions, and full reservation management.

## Live

- Backend: `https://umitdev-flight-backend.onrender.com`
- Swagger: `https://umitdev-flight-backend.onrender.com/swagger/`
- Redoc: `https://umitdev-flight-backend.onrender.com/redoc/`
- Frontend (paired UI): `https://umitdev-flight-frontend.onrender.com`

## Why This Project

This project demonstrates an end-to-end backend workflow that recruiters usually care about:

- Real deployment (Render + Neon)
- Auth + authorization (token, staff vs user)
- Business logic (flight visibility rules, reservation ownership)
- API documentation (Swagger/Redoc)
- Containerized local/prod parity (Docker)

## Core Features

- User registration/login/logout (`dj-rest-auth` + token auth)
- Profile details and password change
- Flights API with role-based visibility
  - Anonymous/user: future/available flights
  - Staff: full flight visibility and CRUD
- Reservation CRUD with multi-passenger support
- Staff-only management screens supported by API permissions
- Swagger and Redoc API docs

## Tech Stack

- Python 3.11
- Django 4.1.7
- Django REST Framework
- PostgreSQL
- drf-yasg (Swagger/Redoc)
- dj-rest-auth + TokenAuthentication
- Docker + Gunicorn + WhiteNoise
- Render (app hosting) + Neon (managed PostgreSQL)

## Architecture

- `backend/` -> Django API service
- `frontend/` -> React client (separate service)
- DB -> Neon PostgreSQL (`flight_app`)

## Main API Endpoints

### Auth

- `POST /users/register/`
- `POST /users/auth/login/`
- `POST /users/auth/logout/`
- `GET /users/auth/user/`
- `POST /users/auth/password/change/`

### Flight

- `GET /flight/flights/`
- `POST /flight/flights/` (staff)
- `GET /flight/flights/{id}/`
- `PUT/PATCH/DELETE /flight/flights/{id}/` (staff)

### Reservation

- `GET /flight/reservations/` (auth)
- `POST /flight/reservations/` (auth)
- `GET /flight/reservations/{id}/` (auth + permission)
- `PUT/PATCH/DELETE /flight/reservations/{id}/` (auth + permission)

## Local Setup (Backend Only)

```bash
cd backend
python3.11 -m venv env311
source env311/bin/activate
python -m pip install -r requirements.txt
python manage.py migrate
python manage.py runserver
```

Backend runs at: `http://127.0.0.1:8000`

## Local Fullstack (Docker)

From project root:

```bash
docker compose up --build -d
docker compose ps
```

Services:

- Backend: `http://127.0.0.1:8000`
- Frontend: `http://localhost:5173`
- PostgreSQL: host `5433` -> container `5432`

## Environment Variables

Use templates only:

- Root backend template: `/.env.example`
- Frontend templates: `/frontend/.env.example`, `/frontend/.env.production.example`

Do not commit real `.env` values.

## Deploy Notes

Current production path:

- Backend -> Render Web Service (Docker)
- Frontend -> Render Web Service (Docker)
- Database -> Neon PostgreSQL

Detailed deployment/runbook:

- `../DEPLOY_CHECKLIST.md`
- `../SMOKE_TEST_CHECKLIST.md`
- `../PROJECT_PROGRESS.md`

## Contact

- GitHub: `https://github.com/Umit8098`
- LinkedIn: `https://linkedin.com/in/umit-arat/`
