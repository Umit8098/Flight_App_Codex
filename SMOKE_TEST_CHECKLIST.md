# Smoke Test Checklist

Bu dosya, frontend + backend temel akislarinin hizli manuel kontrol listesi.

## 1) Baslatma

1. Backend:
   - `cd backend`
   - `source env311/bin/activate`
   - `python manage.py runserver`
2. Frontend:
   - `cd frontend`
   - `yarn dev`

Beklenen:

- Backend `http://127.0.0.1:8000` acik
- Frontend `http://localhost:5173` acik

## 2) Anonymous User

1. Home (`/`) ac.
2. Flight listesi gorunuyor mu kontrol et.
3. `Reserve` butonu gorunmemeli (login yokken).

## 3) Register + Login + Logout

1. `Auth -> Register` ile yeni user olustur.
2. `Auth -> Login` ile giris yap.
3. Browser localStorage'da `flight_app_token` olusuyor mu kontrol et.
4. `Auth -> Logout` ile cikis yap.
5. `flight_app_token` siliniyor mu kontrol et.

## 4) Reservation (Home Modal)

1. Login ol.
2. Home'da bir flight satirinda `Reserve` tikla.
3. Modal aciliyor mu kontrol et.
4. En az bir passenger ile reservation olustur.
5. Basari mesaji geliyor mu kontrol et.

## 5) My Reservations

1. `Auth -> My Reservations` ac.
2. Az once olusturulan reservation listede gorunuyor mu kontrol et.
3. `Go to Flight` ile ilgili flight sayfasina gidilebiliyor mu kontrol et.

## 6) Flight Detail Reservation CRUD

1. `/flights/:id` detail sayfasina git.
2. Reservation create et.
3. Reservation satirinda `Edit` ile guncelle.
4. Reservation satirinda `Delete` ile sil.

Beklenen:

- Islem sonrasi tablo otomatik yenilenir.

## 7) Profile

1. `Auth -> Profile` ac.
2. User bilgileri gorunuyor mu kontrol et.
3. Password change formunu test et.

## 8) Staff Panel (Role Guard)

1. Staff kullanici ile login ol.
2. `Auth -> Staff Panel` gorunmeli.
3. `/staff/flights` sayfasinda flight create/update/delete test et.
4. Non-staff kullanici ile `/staff/flights` acmayi dene.

Beklenen:

- Non-staff home'a yonlendirilmeli.

## 9) API Auth Guard

1. Token olmadan `GET /flight/reservations/` dene.
2. Token ile ayni endpointi dene.

Beklenen:

- Tokensiz istek yetkisiz olmali.
- Tokenli istek data dondurmeli.
