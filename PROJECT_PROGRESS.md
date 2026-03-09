# Project Progress Log

Bu dosya, backend + frontend fullstack gecis surecini adim adim takip etmek icin tutulur.

## 2026-03-04 - Baslangic

1. Django proje clone edildi ve `.git` baglantisi kaldirildi.
2. Ortam hatasi cozuldu: sistemde sadece Python `3.13` oldugu tespit edildi.
3. Python `3.11` kuruldu (`/opt/homebrew/bin/python3.11`).
4. `env311` sanal ortami ile proje ayaga kaldirildi.
5. `pkg_resources` hatasi icin `setuptools` kurulumu yapildi.
6. Migration calistirildi.
7. PostgreSQL baglantisi dogrulandi:
   - ENGINE: `django.db.backends.postgresql_psycopg2`
   - NAME: `Flight_App_CH-12`
8. `dbshell` ve pgAdmin uzerinden tablolarin gorunebilir oldugu dogrulandi.

## 2026-03-04 - Docker Yapilandirma Asamasi

1. Aktif proje klasoru netlestirildi:
   - Proje koku: `Flight_App_CH-12_Codex/`
2. `frontend/` klasoru Vite React ile olusturuldu.
3. Frontend bagimliliklari yuklendi (`npm install`).
4. Backend container dosyalari eklendi:
   - `Dockerfile`
   - `docker/backend-entrypoint.sh`
5. Frontend container dosyalari eklendi:
   - `frontend/Dockerfile`
   - `frontend/docker/nginx.conf`
6. Koke `docker-compose.yml` eklendi (servisler: `backend`, `frontend`, `db`, `redis`).
7. Docker ignore dosyalari eklendi:
   - `.dockerignore`
   - `frontend/.dockerignore`
8. `docker compose config` basariyla calisti.
9. `docker compose up --build -d` denemesinde blokaj:
   - Docker daemon calismiyor (`Cannot connect to the Docker daemon`).
10. Klasor yapisi beklentiye gore duzenlendi:
    - `backend/` (Django + backend Docker dosyalari)
    - `frontend/` (React + frontend Docker dosyalari)
11. `docker-compose.yml` backend build yolu guncellendi:
    - `context: ./backend`

## 2026-03-04 - Frontend-Backend Entegrasyon (Adim 1-4)

1. Backend CORS destegi eklendi:
   - `corsheaders` app ve middleware ayarlari yapildi.
   - `CORS_ALLOWED_ORIGINS` varsayilan olarak:
     - `http://localhost:5173`
     - `http://127.0.0.1:5173`
2. Paketler eklendi:
   - Backend: `django-cors-headers==3.14.0` (Django 4.1.7 ile uyumlu)
   - Frontend: `axios`
3. Frontend API katmani olusturuldu:
   - `frontend/src/api/client.js`
   - `VITE_API_BASE_URL` kullanimi eklendi.
4. Frontend ilk ekran entegre edildi:
   - `frontend/src/App.jsx` Django API'den `/flight/flights/` verisini cekiyor.
   - `frontend/src/index.css` listeleme ekrani icin duzenlendi.
5. Frontend env ornegi eklendi:
   - `frontend/.env.example`

## 2026-03-04 - Backend Inceleme (Auth + API Haritasi)

1. Auth endpointleri hazir:
   - `POST /users/register/`
   - `POST /users/auth/login/`
   - `POST /users/auth/logout/`
   - `GET /users/auth/user/`
2. Token tabanli kimlik dogrulama aktif:
   - `REST_FRAMEWORK.DEFAULT_AUTHENTICATION_CLASSES = TokenAuthentication`
3. Login cevabi custom serializer ile genisletilmis:
   - token + user bilgisi donuyor.
4. Flight endpointleri:
   - `GET/POST /flight/flights/`
   - `GET/PUT/PATCH/DELETE /flight/flights/{id}/`
5. Reservation endpointleri:
   - `GET/POST /flight/reservations/`
   - `GET/PUT/PATCH/DELETE /flight/reservations/{id}/`
6. Rol davranisi:
   - Staff: tum ucuslar
   - Non-staff/anonim: sadece gelecekteki ucuslar
7. UI'yi backend'e dayali kurma karari dogrulandi.

## 2026-03-04 - UI Routing + Auth Toggle + Detail Page

1. Navbar olusturuldu:
   - Solda logo (`UF`)
   - Ortada `Umitdev Flight Reservation`
   - Sagda `Auth` toggle menu (`Login`, `Register`, `Logout`)
2. React Router yapisi kuruldu:
   - `/` Home (flights list)
   - `/flights/:id` Flight detail + reservation detail
   - `/login` Login
   - `/register` Register
3. Home sayfasi role-akisina gore backend verisini kullaniyor:
   - Anonymous / Authenticated / Staff metinleri ve ilgili listeleme
4. Flight detail sayfasi eklendi:
   - Ucus temel detaylari
   - Loginli kullanici icin reservation detaylari
5. Frontend auth akisi eklendi:
   - Login'den gelen token `localStorage`'a kaydediliyor
   - Tum API isteklerine otomatik `Authorization: Token ...` header'i ekleniyor
6. Backend serializer guncellendi:
   - Login/user endpointlerinde `is_staff` alani da donuyor.
7. Derleme/dogrulama:
   - `python manage.py check` basarili
   - `npm run build` basarili

## 2026-03-05 - Reservation Create + Auto Refresh

1. Flight detail sayfasina loginli user icin reservation formu eklendi.
2. Form alanlari:
   - `first_name`
   - `last_name`
   - `email`
   - `phone_number`
3. `POST /flight/reservations/` ile reservation olusturma akisi eklendi.
4. Basarili create sonrasi:
   - form temizleniyor
   - detay listesini ceken fonksiyon tekrar calisiyor
   - reservation tablosu otomatik guncelleniyor
5. Form/success durumlari icin UI iyilestirmeleri yapildi.
6. Dogrulama:
   - `npm run build` basarili

## 2026-03-05 - Multi Passenger Reservation

1. Reservation create formu tek yolcudan coklu yolcuya cevrildi.
2. Kullanici `+ Add Passenger` ile yeni yolcu ekleyebiliyor.
3. Her yolcu karti icin `Remove` butonu eklendi (en az bir yolcu kalir).
4. Form submit'te tum yolcular ayni reservation altinda backend'e gonderiliyor.
5. UI tarafinda yolcu kartlari ve ikincil buton stilleri eklendi.
6. Dogrulama:
   - `npm run build` basarili

## 2026-03-05 - Reservation Update/Delete

1. Backend:
   - `ReservationSerializer` icine `update()` metodu eklendi.
   - Update'te passenger listesi degistiginde eski passenger kayitlari temizlenip yenileri yaziliyor.
2. Guvenlik:
   - `ReservationView` icin `permission_classes = (IsAuthenticated,)` eklendi.
3. Frontend detail tablosu:
   - Her reservation satirina `Edit` ve `Delete` aksiyonlari eklendi.
4. Edit akisi:
   - Secilen reservation yolculari forma yukleniyor.
   - Form submit `PUT /flight/reservations/{id}/` ile guncelliyor.
5. Delete akisi:
   - Onay penceresi sonrasi `DELETE /flight/reservations/{id}/`.
   - Silme/guncelleme sonrasi liste otomatik yenileniyor.
6. Dogrulama:
   - `python manage.py check` basarili
   - `npm run build` basarili

## 2026-03-06 - Staff Flight CRUD

1. Staff icin yeni sayfa eklendi:
   - Route: `/staff/flights`
   - Dosya: `frontend/src/pages/StaffFlightsPage.jsx`
2. CRUD ozellikleri:
   - `POST /flight/flights/` create
   - `PUT /flight/flights/{id}/` update
   - `DELETE /flight/flights/{id}/` delete
3. UI ozellikleri:
   - Flight formu (create/edit ayni form)
   - Flight tablo listesi
   - Satir bazli Edit/Delete aksiyonlari
4. Role guard:
   - Staff degilse `/staff/flights` route'u home'a yonleniyor.
5. Navbar:
   - Staff user icin `Staff Panel` linki eklendi.
6. Home liste senkronu:
   - Staff panelde degisiklikten sonra home listesi de yenileniyor.
7. Dogrulama:
   - `npm run build` basarili

## 2026-03-06 - My Reservations + Profile/Auth

1. `My Reservations` sayfasi eklendi:
   - Route: `/my-reservations`
   - Endpoint: `GET /flight/reservations/`
   - Kullanici tum reservation'larini tek sayfada gorebiliyor.
2. `Profile` sayfasi eklendi:
   - Route: `/profile`
   - User bilgisi (`/users/auth/user/`) UI'da gosteriliyor.
3. Password change UI eklendi:
   - Endpoint: `POST /users/auth/password/change/`
   - Alanlar: `old_password`, `new_password1`, `new_password2`
4. Role-based menu/guard duzeni:
   - Auth dropdown'a `My Reservations`, `Profile`, (staff ise) `Staff Panel` linkleri eklendi.
   - Login olmayan user bu route'lara gidemez, login sayfasina yonlenir.
   - Staff olmayan user `/staff/flights` sayfasina gidemez, home'a yonlenir.
5. Dogrulama:
   - `npm run build` basarili

## 2026-03-06 - Home Reservation Modal

1. Home tablosunda loginli user icin `Reserve` butonu modal acacak sekilde degistirildi.
2. Modal icinde:
   - secili flight ozeti
   - coklu passenger formu
   - `POST /flight/reservations/` create akisi
3. Detail sayfasina gitmeden reservation olusturma imkani eklendi.
4. `flight detail alinamadi` durumu icin not:
   - Bu hata genelde secilen flight, kullanicinin gecerli queryset'inde degilse olur
   - (gecmis tarih/saat filtreleri nedeniyle).
5. Dogrulama:
   - `npm run build` basarili

## 2026-03-06 - Flight Detail Access Fix (User)

1. Problem:
   - User home listede gordugu flight'in detail sayfasinda bazen `Flight detail alinamadi` hatasi aliyordu.
2. Neden:
   - Non-staff queryset `union()` ile kuruluydu; detail retrieve akisinda kararsiz davranisa yol aciyordu.
3. Cozum:
   - `FlightView.get_queryset()` icinde `union` kaldirildi.
   - Tek bir `Q(...)` filtresi ile "gelecek ucuslar" kosulu tanimlandi.
4. Sonuc:
   - User home'da gordugu flight'in detail sayfasina tutarli sekilde gidebilir.
5. Dogrulama:
   - `python manage.py check` basarili

## 2026-03-06 - Delivery Docs Update

1. Proje dokumantasyonlari guncellendi:
   - `PROJECT_PROGRESS.md`
   - `DEPLOY_CHECKLIST.md`
   - `SMOKE_TEST_CHECKLIST.md`
2. Proje kokune `SMOKE_TEST_CHECKLIST.md` eklendi:
   - Baslatma
   - Auth akislar
   - Reservation akislar
   - Staff panel guard
   - API auth guard kontrolleri

## 2026-03-06 - Smoke Test (Automated Backend Part)

1. Servis kontrolu:
   - Backend `127.0.0.1:8000` dinlemede
   - Frontend `localhost:5173` dinlemede
2. API temel check:
   - `GET /flight/flights/` -> `200`
3. Auth akisi curl ile test edildi:
   - Register -> token olustu
   - Login -> key dondu
   - `GET /users/auth/user/` token ile -> `200`
   - `GET /flight/reservations/` tokensiz -> `401`
   - `GET /flight/reservations/` tokenli -> `200`
   - Logout -> `200`
   - Logout sonrasi `GET /users/auth/user/` -> `401`

## 2026-03-06 - Staff Reservations UI Visibility

1. UI netlestirme yapildi:
   - Staff kullaniciya ozel `All Reservations` menusu eklendi.
   - Route: `/staff/reservations` (staff only)
2. `MyReservationsPage` staff/non-staff baslik/mod aciklamasi alacak sekilde guncellendi.
3. Route guard:
   - Staff degilse `/staff/reservations` home'a yonlenir.
4. Dogrulama:
   - `npm run build` basarili

## 2026-03-06 - Deploy Prep (Render/Railway Single Platform)

1. Backend production host/security ayarlari env tabanli hale getirildi:
   - `ALLOWED_HOSTS` -> env (`Csv`)
   - `CSRF_TRUSTED_ORIGINS` -> env (`Csv`)
   - `prod.py` icinde `DEBUG` env'den okunuyor (default `False`)
2. Env template dosyalari eklendi:
   - `.env.example` (proje koku)
   - `frontend/.env.production.example`
3. Deploy adimlari dokumante edildi:
   - `DEPLOY_CHECKLIST.md` (Render/Railway backend+frontend+DB)
4. Dogrulama:
   - `python manage.py check` basarili
   - `npm run build` basarili
5. Not:
   - Docker full run adimi, bu ortamda daemon kapali oldugu icin terminalden tamamlanamadi.

## 2026-03-06 - Docker Full Run (Local) Tamamlandi

1. Docker compose port cakismasi duzeltildi:
   - `docker-compose.yml` icinde PostgreSQL host portu `5432:5432` -> `5433:5432` yapildi.
2. Backend container acilis hatasi duzeltildi:
   - `backend/Dockerfile` icinde `setuptools<81` sabitlendi (`drf_yasg` icin `pkg_resources` gereksinimi).
3. Docker rebuild + up basarili:
   - `docker compose up --build -d`
4. Container durumlari:
   - `flight_db` up (`5433->5432`)
   - `flight_backend` up (`8000->8000`)
   - `flight_frontend` up (`5173->80`)
   - `flight_redis` up (`6379->6379`)
5. Backend ic saglik kontrolu:
   - `docker compose exec -T backend python manage.py check` -> basarili.

## 2026-03-08 - Guncel Durum

1. Monorepo yapi tamam:
   - `backend/`
   - `frontend/`
2. Docker yapisi tamam:
   - Backend + frontend Dockerfile
   - `docker-compose.yml` (`backend + frontend + db + redis`)
3. Deploy hedefi tamam:
   - Backend: Render
   - Frontend: Render
   - Database: Neon PostgreSQL (`flight_app`)
4. Local + cloud calisma dogrulandi:
   - Local fullstack calisiyor
   - Render/Neon fullstack calisiyor

## Teknoloji Kisa Notlari

1. Docker:
   Uygulamayi "container" icinde paketleyip her ortamda ayni sekilde calistirmayi saglar.
2. Dockerfile:
   Bir container'in nasil olusturulacagini anlatan tarif dosyasi.
3. Docker Compose:
   Birden fazla servisi (backend, frontend, db gibi) tek komutla birlikte calistirir.
4. Gunicorn:
   Django uygulamasini production modunda servis eden uygulama sunucusu.
5. Nginx:
   Web sunucusu/reverse proxy; static dosyalari hizli servis eder, istekleri backend'e yonlendirir.
6. PostgreSQL:
   Guclu iliskisel veritabani; bu projede ana DB olarak kullanilacak.
7. Redis:
   Bellek uzerinde hizli veri saklar; cache ve background job kuyrugu icin kullanilir (opsiyonel).
8. Render:
   Cloud uzerinde backend ve database deploy etmeyi kolaylastiran platform.
9. Railway:
   Render benzeri, hizli servis ve DB deploy platformu.
10. Vercel:
    Frontend projelerini hizli deploy etmek icin yaygin platform.
11. Netlify:
    Frontend deploy icin baska bir yaygin platform (senin asina oldugun secenek).
12. Managed PostgreSQL:
    DB kurulum/bakim islemlerini servis saglayici ustlenir (backup, update, uptime kolaylasir).
13. Docker Daemon:
    Docker engine'in arka planda calisan servisidir; container calistirmak icin acik olmasi gerekir.
14. CORS (Cross-Origin Resource Sharing):
    Frontend ve backend farkli port/domain'de calisinca tarayiciya hangi origin'lerin API'ye erisebilecegini soyler.
15. Axios:
    Frontend tarafinda HTTP istekleri icin kullanilan populer kutuphane.
16. React Router:
    SPA icinde sayfa gecislerini URL tabanli yoneten kutuphane.
17. localStorage:
    Tarayicida kalici key-value veri saklama alani; token tutmak icin kullanildi.

## Notlar

1. Su an bu dosya mevcut workspace icinde olusturuldu.
2. Bundan sonra her adim tamamlandiginda bu log guncellenecek.
3. Bu dosyada yeni teknoloji gecerse, kisa aciklamasi da ayni bolume eklenecek.
4. Mevcut ana proje dizini artik:
   - `Flight_App_CH-12_Codex/`

## 2026-03-08 - Phone Number Field Fix

1. `Passenger.phone_number` alani `IntegerField` -> `CharField(max_length=20)` olarak degistirildi.
2. Yeni migration olusturuldu:
   - `flight/migrations/0004_alter_passenger_phone_number.py`
3. Dogrulama:
   - `python manage.py check` basarili.

## 2026-03-08 - Admin Static/CSS Fix (Render Prod)

1. WhiteNoise middleware eklendi.
2. Static ayarlari eklendi:
   - `STATIC_ROOT`
   - `STATICFILES_STORAGE=CompressedManifestStaticFilesStorage`
3. Backend startup akisina `collectstatic --noinput` eklendi.
4. Docker build icin `whitenoise` paketi eklendi.

## 2026-03-08 - Frontend UI Polish

1. Global tema yenilendi (`frontend/src/index.css`):
   - Yeni renk paleti, tipografi, arka plan gradyanlari
   - Navbar, tablo, kart, form ve modal stilleri modernlestirildi
   - Mobil responsive düzen korundu ve iyilestirildi
2. Reservation form telefon alani backend degisikligiyle uyumlu hale getirildi:
   - `type=\"number\"` -> `type=\"tel\"`
   - API payload telefon degeri `String` olarak gonderilecek sekilde guncellendi
3. Dogrulama:
   - `npm run build` basarili.
4. Ek polish:
   - Home/My Reservations/Staff Flights icin loading skeleton gorunumu eklendi.
   - Bos liste durumlari icin `empty-state` gorunumu eklendi.
   - Header alt metinleri icin `muted` sinifi eklendi.

## 2026-03-09 - README + Env Template Standardization

1. Proje kokune yeni `README.md` eklendi (monorepo, local run, docker run, deploy ozet).
2. `.env.example` gercek secret icermeyecek sekilde production template formatinda guncellendi.
3. `frontend/.env.example` ve `frontend/.env.production.example` dosyalari acik ve tutarli aciklamalarla guncellendi.
4. `backend/Readme.md` recruiter-odakli ve kisa formatta bastan yazildi:
   - net live linkler
   - teknoloji ve mimari ozeti
   - endpoint ozetleri
   - local/docker run adimlari
