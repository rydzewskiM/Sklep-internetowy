# Sklep internetowy z książkami — pełny system wdrożeniowy

To jest kompletna aplikacja full-stack:
- **frontend**: React + Vite
- **backend**: Node.js + Express + Prisma
- **baza danych**: PostgreSQL
- **autoryzacja administratora**: JWT + hashowanie haseł bcrypt
- **PDF zamówienia**: generowany po opłaceniu zamówienia po stronie frontendu
- **panel administracyjny**: logowanie, zarządzanie stanami magazynowymi, zmiana statusu zamówień

System wykorzystuje bazę książek z załączonego pliku oraz proces realizacji zamówienia oparty na załączonej karcie procesu.

## Moduły

### Strefa klienta
- wyszukiwanie książek po nazwie i autorze
- filtrowanie po gatunku i dostępności
- koszyk zakupowy
- checkout i zapis zamówienia do bazy
- status realizacji zamówienia:
  - zamówienie w trakcie realizacji
  - zamówienie spakowane
  - zamówienie wysłane
- generowanie PDF zamówienia

### Strefa administratora
- logowanie administratora
- dashboard magazynu
- edycja stanów produktów
- przegląd zamówień
- zmiana statusów realizacji
- podgląd klientów i pozycji zamówień

## Dane logowania administratora

Po uruchomieniu seeda:
- **email:** admin@bookstore.local
- **hasło:** Admin123!

## Uruchomienie przez Docker

```bash
docker compose up --build
```\n\nPlik `docker-compose.yml` w tej poprawionej wersji:\n- nie używa już przestarzałego pola `version`,\n- czeka na gotowość PostgreSQL dzięki `healthcheck`,\n- uruchamia `prisma db push`, a potem seed administratora i książek.

Aplikacje będą dostępne pod adresami:
- frontend: `http://localhost:5173`
- backend API: `http://localhost:4000`

## Uruchomienie lokalne bez Dockera

### 1. Backend
```bash
cd backend
cp .env.example .env
npm install
npm run prisma:generate
npx prisma db push
npm run prisma:seed
npm run dev
```

### 2. Frontend
W drugim terminalu:
```bash
cd frontend
cp .env.example .env
npm install
npm run dev
```

## Zmienne środowiskowe

### backend/.env
```env
PORT=4000
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/bookstore_db?schema=public
JWT_SECRET=super_secret_change_me
ADMIN_SEED_EMAIL=admin@bookstore.local
ADMIN_SEED_PASSWORD=Admin123!
FRONTEND_ORIGIN=http://localhost:5173
```

### frontend/.env
```env
VITE_API_URL=http://localhost:4000/api
```

## Migracje i seed

Po pierwszym uruchomieniu należy wykonać:
```bash
npx prisma db push
npm run prisma:seed
```

Seeder:
- tworzy konto administratora,
- importuje **1000 książek** z załączonej bazy danych,
- ustawia początkowe stany magazynowe.

## Architektura

- `backend/src/routes/auth.js` — logowanie administratora
- `backend/src/routes/books.js` — katalog książek + admin update stock
- `backend/src/routes/orders.js` — tworzenie zamówień, lista zamówień, zmiana statusu
- `backend/prisma/schema.prisma` — modele bazy danych
- `frontend/src/App.jsx` — aplikacja klienta i panel admina
- `frontend/src/lib/api.js` — komunikacja z API

## Ważna uwaga prawna

Projekt generuje **potwierdzenie zamówienia PDF / wydruk niefiskalny**.
Aby wystawiać **prawnie poprawny paragon fiskalny w Polsce**, trzeba zintegrować aplikację z certyfikowanym rozwiązaniem fiskalnym lub urządzeniem fiskalnym.

## Status

To jest gotowy szkielet wdrożeniowy z pełnym przepływem aplikacyjnym, bazą danych i logowaniem administratora, przygotowany do dalszego wdrożenia produkcyjnego.
