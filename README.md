# ERAMUS - Sistema Gestionale

Applicazione web full-stack per la gestione di utenti, ruoli e inventario di un negozio.

Backend: https://github.com/AngeloRusu24/EramusBE
Frontend: https://github.com/AngeloRusu24/EramusFE

---

## Stack Tecnologico

- **Frontend:** Next.js 14 (App Router) + TypeScript + Bootstrap Italia
- **Backend:** Ruby on Rails 8 (API REST) + JWT
- **Database:** PostgreSQL

---

## Credenziali Admin

- **Username:** `admin`
- **Password:** `Admin123!`

---

## BACKEND (EramusBE)

### Struttura cartelle

```
EramusBE/
├── .env                          ← variabili d'ambiente (non su GitHub)
├── Gemfile                       ← dipendenze Ruby
├── db/
│   └── schema.sql                ← schema PostgreSQL con tutte le tabelle
├── config/
│   ├── routes.rb                 ← definizione degli endpoint API
│   ├── database.yml              ← configurazione connessione PostgreSQL
│   └── initializers/
│       └── cors.rb               ← configurazione CORS per il frontend
└── app/
    ├── models/
    │   ├── utente.rb
    │   ├── ruolo.rb
    │   ├── prodotto.rb
    │   ├── tipo_prodotto.rb
    │   ├── movimento_magazzino.rb
    │   ├── log_accesso.rb
    │   ├── recupero_password.rb
    │   └── notifica_email.rb
    ├── controllers/
    │   ├── application_controller.rb     ← autenticazione JWT base
    │   └── api/v1/
    │       ├── auth_controller.rb        ← login, refresh, recupero password
    │       ├── utenti_controller.rb      ← CRUD utenti
    │       ├── prodotti_controller.rb    ← CRUD prodotti
    │       ├── movimenti_controller.rb   ← movimenti magazzino
    │       ├── dashboard_controller.rb   ← statistiche dashboard
    │       ├── tipo_prodotto_controller.rb
    │       └── ruoli_controller.rb
    ├── services/
    │   └── jwt_service.rb               ← generazione e validazione JWT
    └── mailers/
        └── utente_mailer.rb             ← email benvenuto e recupero password
```

### File .env

Crea il file `.env` nella root di `EramusBE/` con:

```
DB_USERNAME=postgres
DB_PASSWORD=tuapassword
JWT_SECRET_KEY=eramus_jwt_secret_key_molto_lunga_2026
FRONTEND_URL=http://localhost:3000
```

### Setup da zero

```bash
# 1. Entra nella cartella
cd EramusBE

# 2. Installa le gem
bundle install

# 3. Avvia PostgreSQL (se non è avviato)
sudo systemctl start postgresql

# 4. Crea il database
rails db:create

# 5. Esegui lo schema SQL
psql -U postgres -d eramus_be_development -f db/schema.sql

# 6. Avvia il server
rails s -p 3001
```

### Creare l'utente Admin

```bash
rails console
```

```ruby
Utente.create!(
  username: 'admin',
  email: 'admin@eramus.it',
  password: 'Admin123!',
  nome: 'Admin',
  cognome: 'Sistema',
  ruolo: Ruolo.find_by(nome: 'Admin')
)
```

### Endpoint API

| Metodo | URL | Descrizione | Auth |
|--------|-----|-------------|------|
| POST | /api/v1/auth/login | Login | No |
| POST | /api/v1/auth/refresh | Refresh token | No |
| POST | /api/v1/auth/forgot_password | Richiedi reset password | No |
| POST | /api/v1/auth/reset_password | Reset password | No |
| GET | /api/v1/dashboard | Dati dashboard | Sì |
| GET | /api/v1/utenti | Lista utenti | Admin |
| POST | /api/v1/utenti | Crea utente | Admin |
| PATCH | /api/v1/utenti/:id | Modifica utente | Admin |
| DELETE | /api/v1/utenti/:id | Disattiva utente (soft delete) | Admin |
| PATCH | /api/v1/utenti/:id/assign_role | Assegna ruolo | Admin |
| GET | /api/v1/prodotti | Lista prodotti | Sì |
| POST | /api/v1/prodotti | Crea prodotto | Sì |
| PATCH | /api/v1/prodotti/:id | Modifica prodotto | Sì |
| DELETE | /api/v1/prodotti/:id | Elimina prodotto (soft delete) | Sì |
| GET | /api/v1/movimenti | Lista movimenti | Sì |
| POST | /api/v1/movimenti | Crea movimento | Sì |
| GET | /api/v1/tipo_prodotto | Lista tipi prodotto | Sì |
| GET | /api/v1/ruoli | Lista ruoli | Sì |

### Funzionalità Backend

- **JWT** con Access Token (15 min) e Refresh Token (7 giorni)
- **Blocco automatico** account dopo 5 tentativi di login falliti
- **Recupero password** via email con link temporaneo (valido 1 ora)
- **Log accessi** con IP e esito (Successo/Fallito)
- **Soft delete** su utenti e prodotti (non vengono eliminati dal DB)
- **Validazione password AGID**: min 8 caratteri, 1 maiuscola, 1 numero, 1 carattere speciale
- **Aggiornamento automatico quantità** prodotto ad ogni movimento magazzino

---

# ERAMUS - Frontend (EramusFE)

Interfaccia web sviluppata con Next.js 14 (App Router) e TypeScript.

---

## Stack Tecnologico

- **Next.js 14** (App Router)
- **TypeScript**
- **Bootstrap Italia** per lo stile grafico
- **Axios** per le chiamate API con gestione automatica JWT
- **Recharts** per i grafici nella dashboard
- **js-cookie** per la gestione dei token

---

## Struttura Cartelle

```
EramusFE/
├── .env.local                    ← URL del backend (non su GitHub)
├── declarations.d.ts             ← supporto import CSS in TypeScript
├── lib/
│   ├── api.ts                    ← client Axios con interceptor JWT
│   └── auth.ts                   ← funzioni login, logout, getUtenteCorrente
└── app/
    ├── layout.tsx                ← layout base con Bootstrap Italia
    ├── login/
    │   └── page.tsx              ← pagina di login
    ├── dashboard/
    │   └── page.tsx              ← dashboard con statistiche e grafico
    ├── utenti/
    │   └── page.tsx              ← gestione utenti
    ├── inventario/
    │   └── page.tsx              ← gestione inventario
    ├── movimenti/
    │   └── page.tsx              ← lista movimenti magazzino
    ├── ruoli/
    │   └── page.tsx              ← gestione ruoli (solo Admin)
    └── reset-password/
        └── page.tsx              ← pagina reset password
```

---

## File .env.local

Crea il file `.env.local` nella root di `EramusFE/` con:

```
NEXT_PUBLIC_API_URL=http://localhost:3001/api/v1
```

> Il file `.env.local` è già nel `.gitignore` e non verrà mai caricato su GitHub.

---

## Setup da Zero

```bash
# 1. Entra nella cartella
cd EramusFE

# 2. Installa le dipendenze
npm install

# 3. Avvia il server
npm run dev
```

Il frontend sarà disponibile su `http://localhost:3000`

---

## Riavvio dopo spegnimento PC

```bash
cd ~/GitHub/Eramus/EramusFE
npm run dev
```

> Assicurati che il backend sia avviato prima di usare il frontend.

---

## Pagine

| URL | Descrizione |
|-----|-------------|
| /login | Maschera di login con validazione AGID e recupera password |
| /dashboard | Statistiche, ultimi movimenti e grafico prodotti per categoria |
| /utenti | Gestione utenti con paginazione, ricerca e CRUD (solo Admin) |
| /inventario | Gestione prodotti con filtri, ordinamento e movimenti magazzino |
| /movimenti | Lista ultimi movimenti di magazzino |
| /ruoli | Gestione ruoli (solo Admin) |
| /reset-password | Pagina reset password tramite link email |

---

## Funzionalità

- **Refresh automatico** del JWT token scaduto tramite interceptor Axios
- **Protezione route**: redirect automatico a /login se non autenticato
- **Validazione password AGID** lato frontend prima dell'invio
- **Paginazione** su utenti e prodotti
- **Ricerca** per username/email (utenti) e nome (prodotti)
- **Filtro per tipo** prodotto (Buste/Carta/Toner)
- **Ordinamento** per prezzo e quantità
- **Evidenziazione** prodotti sotto soglia minima (riga gialla)
- **Grafico** prodotti per categoria con Recharts
- **Solo Admin** può accedere alla gestione utenti e ruoli

---

## Note importanti

- Il file `.env.local` non va mai committato su GitHub
- PostgreSQL deve essere avviato prima di avviare il backend: `sudo systemctl start postgresql`
- Il backend gira su porta **3001**, il frontend su porta **3000**
- Per riavviare tutto dopo un riavvio del PC:

```bash
# Terminale 1 - Backend
sudo systemctl start postgresql
cd ~/GitHub/Eramus/EramusBE
rails s -p 3001

# Terminale 2 - Frontend
cd ~/GitHub/Eramus/EramusFE
npm run dev
```

---

## Credenziali Admin

- **Username:** `admin`
- **Password:** `Admin123!`
