# Deploy Checklist (Render/Railway - Single Platform)

Bu dosya, secilen hedefe gore su plani uygular:

1. deployment env checklist (`.env` local/prod ayrimi)
2. Docker full run dogrulama (`docker compose up --build`)
3. Hedef secimi: Render/Railway (backend + frontend + DB)
4. Frontend `VITE_API_BASE_URL` ayari
5. Backend `CORS/ALLOWED_HOSTS` production domain ayari
6. Canli smoke test

---

## 1) Env Ayrimi (Local vs Prod)

### Local

- Kendi `.env` dosyani localde kullan.
- `ENV_NAME=dev` veya local test ihtiyacina gore `prod`.
- `CORS_ALLOWED_ORIGINS` local frontend URL'lerini icermeli.

### Production

- Bu repodaki `.env.example` dosyasini referans al.
- Platform panelinde env var olarak gir:
  - `SECRET_KEY`
  - `ENV_NAME=prod`
  - `DEBUG=False`
  - `ALLOWED_HOSTS`
  - `CSRF_TRUSTED_ORIGINS`
  - `CORS_ALLOWED_ORIGINS`
  - `SQL_*` (DB bilgileri)

Not: Production secrets dosyaya yazilmaz, platform env panelinde tutulur.

---

## 2) Docker Full Run (Local)

Docker daemon acikken proje kokunde:

```bash
docker compose up --build -d
docker compose ps
```

Not:
- Makinede zaten yerel PostgreSQL `5432` portunu kullaniyorsa compose DB map'i `5433:5432` olmalidir.

Beklenen:

- backend, frontend, db, redis servisleri `Up`
- frontend: `http://localhost:5173`
- backend: `http://127.0.0.1:8000/swagger/`

---

## 3) Platform Kurulumu (Render/Railway)

Tek platformda 3 servis mantigi:

1. `backend` web service
2. `frontend` web/static service
3. `postgres` managed database

### Backend service

- Source repo: bu monorepo
- Root: `backend/`
- Dockerfile: `backend/Dockerfile`
- Env vars: production degerleri

### Database service

- Managed PostgreSQL olustur
- DB connection bilgilerini backend env var'larina bagla

### Frontend service

- Root: `frontend/`
- Build: `npm ci && npm run build`
- Serve: static (platform secenegine gore)
- Env: `VITE_API_BASE_URL=https://<backend-domain>`

---

## 4) Frontend Prod API URL

Production frontend env degeri:

```env
VITE_API_BASE_URL=https://<backend-domain>
```

`frontend/.env.production.example` referans dosyadir.

---

## 5) Backend Domain Ayarlari

Production backend env var'larinda:

```env
ALLOWED_HOSTS=<backend-domain>,<gerekliyse-ek-domain>
CSRF_TRUSTED_ORIGINS=https://<backend-domain>,https://<frontend-domain>
CORS_ALLOWED_ORIGINS=https://<frontend-domain>
```

---

## 6) Canli Smoke Test (Prod URL)

1. Frontend aciliyor mu?
2. Register/Login/Logout calisiyor mu?
3. Home'dan reservation modal create calisiyor mu?
4. My Reservations listesi geliyor mu?
5. Profile'da password change calisiyor mu?
6. Staff ile:
   - Staff Panel (Flight CRUD)
   - All Reservations sayfasi
7. Non-staff ile `/staff/*` route guard calisiyor mu?

API hizli kontrol:

```bash
curl -i https://<backend-domain>/flight/flights/
curl -i https://<backend-domain>/swagger/
```

---

## Notlar

- Bu projede frontend backend'e token auth ile baglanir.
- `django-debug-toolbar` production'da acik olmamali.
- Deploy sonrasi ilk islem olarak smoke test checklist tamamlansin.
