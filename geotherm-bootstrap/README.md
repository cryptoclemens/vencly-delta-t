# Geotherm by Vencly

![Status](https://img.shields.io/badge/status-Bootstrap-blue) ![Stack](https://img.shields.io/badge/stack-Vite%20%7C%20React%2019%20%7C%20Tailwind%204-2E75B6) ![License](https://img.shields.io/badge/license-proprietary-gray)

**Geotherm** ist die modulare Geothermie-Suite der [vencly GmbH](https://www.vencly.com). Eine einheitliche Web-Plattform, die alle Werkzeuge entlang des Geothermie-Projektlebenszyklus in einem Workflow bündelt — **vom Standort zum Bohrplan**.

🌐 **Live (geplant):** [geotherm.vencly.com](https://geotherm.vencly.com)

---

## 🧭 Orientierung für Claude Code / neue Entwickler

Wenn du eine neue Claude-Code-Sitzung startest, lies die Dokumente in dieser Reihenfolge:

1. **[BRIEF.md](BRIEF.md)** – Produktvision, Zielgruppe, Positionierung, strategische Leitplanken
2. **[Tasks.md](Tasks.md)** – Detaillierter Backlog mit Milestones 0–7 und Abschluss-Kriterien
3. **[CLAUDE.md](CLAUDE.md)** – Konventionen, Architektur-Regeln, Do's & Don'ts *(wird in M1 erstellt)*
4. **Diese README** – Technisches Setup und How-to-Run

Die erste Session beginnt mit **Milestone 1 (Projekt-Setup)** aus Tasks.md.

---

## 📦 Was steckt drin?

Geotherm ist ein **App-Container** mit mehreren modularen **In-Apps**:

| In-App | Route | Beschreibung | Status |
|---|---|---|---|
| **GPA** – Geothermie-Potenzial-Atlas | `/atlas` | Interaktive Karte des norddeutschen Tieflandes mit Fernwärme-, Geologie- und Wärmequellen-Overlays | Migration aus [geopotatlas](https://github.com/cryptoclemens/geopotatlas) |
| **DeltaT** – Dubletten-Auslegungsrechner | `/deltat` | Echtzeit-Rechner für geothermische Dubletten-Systeme mit WP-Dimensionierung | Migration aus [vencly-delta-t](https://github.com/cryptoclemens/vencly-delta-t) |

Beide In-Apps tauschen Daten über einen gemeinsamen **Workspace-Store** aus. Weitere In-Apps sind in Planung (siehe [BRIEF.md §2.4](BRIEF.md)).

---

## 🏗 Architektur-Prinzipien

### 1. Modulare In-Apps

Jede In-App lebt in einem eigenen Unterordner unter `src/apps/` und hat:

- Eigenen Components-Ordner
- Eigenen Zustand-Store
- Eigenen DB-Schema-Namespace (`gpa_*`, `deltat_*`, …)
- Eine `index.jsx`-Entry-Point, die via `React.lazy()` geladen wird

**Konsequenz:** Jede In-App kann ohne die anderen laufen und später als eigenständiges Whitelabel-Produkt extrahiert werden.

### 2. Cloud-agnostisch von Tag 1

- **Keine hardcoded URLs** — alle Backend-Endpunkte kommen aus `VITE_*`-Env-Vars
- **Eigener Auth-Adapter** (`src/core/auth/useAuth.js`) statt direkter Supabase-Calls
- **REST-only** — keine Supabase-spezifischen Features (Realtime, Storage-Buckets)
- **Dockerfile + docker-compose.yml** von Anfang an vorhanden
- **Portables DB-Schema** — reines Postgres, keine Extensions

→ Migration von Vercel/Supabase zu einer Hetzner-VM darf max. **eine Woche** dauern.

### 3. Shared Core

```
src/core/
├── auth/      # useAuth(), AuthProvider, RequireAuth
├── api/       # REST-Client mit Interceptors
├── ui/        # Button, Modal, Input, ParamSlider, etc. (shared)
├── layout/    # Shell, Header, Sidebar, Footer
└── router/    # React Router + In-App-Registry
```

### 4. Wissenschaftliche Validität

Jede Formel im Rechner-Kern hat eine Quellenangabe (VDI, DIN, EN, peer-reviewed Paper). `PLAUSI_CHECK.md` listet alle Formeln mit Review-Status. Unit-Tests decken die Berechnungslogik ab.

---

## 🚀 Quick Start

> **Hinweis:** Dieses Repo ist aktuell im **Bootstrap-Status**. Die folgenden Befehle funktionieren erst ab Milestone 1.

```bash
# Voraussetzung
node --version   # mind. 20.x
npm --version    # mind. 10.x

# 1. Klonen
git clone https://github.com/cryptoclemens/geotherm.git
cd geotherm

# 2. Dependencies
npm install

# 3. Env-Variablen
cp .env.example .env.local
# .env.local öffnen und mit deinen Supabase-Credentials befüllen

# 4. Dev-Server
npm run dev
# → http://localhost:5173

# 5. Production Build
npm run build
npm run preview
```

---

## 🔧 Environment Variables

Alle Env-Vars haben einen `VITE_`-Präfix, damit Vite sie ins Frontend-Bundle injiziert.

| Variable | Pflicht | Beschreibung | Beispiel |
|---|---|---|---|
| `VITE_API_URL` | ✅ | Basis-URL des Backends (Supabase oder eigenes) | `https://xxxx.supabase.co` |
| `VITE_AUTH_URL` | ✅ | Auth-Endpoint (bei Supabase identisch zu API_URL) | `https://xxxx.supabase.co/auth/v1` |
| `VITE_ANON_KEY` | ✅ | Öffentlicher Anon-Key (Publishable oder JWT) | `eyJ...` |
| `VITE_APP_URL` | ❌ | Basis-URL der App (für OAuth-Redirects) | `https://geotherm.vencly.com` |
| `VITE_SENTRY_DSN` | ❌ | Sentry-DSN für Error-Tracking | `https://...@sentry.io/...` |
| `VITE_PLAUSIBLE_DOMAIN` | ❌ | Plausible Analytics Domain | `geotherm.vencly.com` |

Die `.env.example`-Datei wird in Milestone 1 angelegt und sollte **immer** committet werden. Die `.env.local` **niemals**.

---

## 🗺 Projektstruktur (Ziel nach Milestone 4)

```
geotherm/
├── public/
│   ├── favicon.svg
│   └── vencly.png
├── src/
│   ├── apps/                # Modulare In-Apps
│   │   ├── gpa/             # Geothermie-Potenzial-Atlas
│   │   │   ├── components/
│   │   │   ├── store/
│   │   │   ├── data/
│   │   │   └── index.jsx
│   │   └── deltat/          # DeltaT Rechner
│   │       ├── components/
│   │       ├── store/
│   │       ├── calc/        # Pure Berechnungslogik + Unit-Tests
│   │       └── index.jsx
│   ├── core/                # Gemeinsame Infrastruktur
│   │   ├── auth/
│   │   ├── api/
│   │   ├── ui/
│   │   ├── layout/
│   │   └── router/
│   ├── pages/               # Marketing + Legal
│   │   ├── Landing.jsx
│   │   ├── Impressum.jsx
│   │   ├── Datenschutz.jsx
│   │   ├── AGB.jsx
│   │   └── Security.jsx
│   ├── hooks/
│   ├── store/               # Globaler Store (User, Theme, Workspace)
│   ├── styles/
│   ├── App.jsx
│   └── main.jsx
├── .env.example
├── Dockerfile               # Multi-Stage-Build für Hetzner
├── docker-compose.yml       # App + Postgres + Backups
├── nginx.conf               # SPA-Fallback + Security-Headers
├── vite.config.js
├── tailwind.config.js
├── package.json
├── BRIEF.md                 # Produktvision
├── Tasks.md                 # Backlog mit Milestones
├── README.md                # Du bist hier
├── CLAUDE.md                # Konventionen für Claude Code
├── PLAUSI_CHECK.md          # Wissenschaftlicher Formeln-Review
├── CHANGELOG.md
└── .github/workflows/
    └── deploy.yml           # Vercel-Deploy via GitHub Actions
```

---

## 🔐 Auth-Flow

Geotherm nutzt **Supabase Auth** als initialer Provider, ist aber so gebaut, dass ein Wechsel zu **Keycloak** auf Hetzner trivial ist.

```
┌──────────────┐
│  Component   │
│  (z.B.       │
│   Login.jsx) │
└──────┬───────┘
       │ useAuth()
       ▼
┌──────────────┐
│ AuthProvider │  ← einziger Ort, wo Supabase/Keycloak importiert wird
└──────┬───────┘
       │
       ▼
┌──────────────┐          ┌──────────────┐
│  Supabase    │   oder   │   Keycloak   │
│  (heute)     │          │   (später)   │
└──────────────┘          └──────────────┘
```

**Wichtig:** Keine Komponente ruft `supabase.auth.*` direkt auf. Immer über `useAuth()`.

### Unterstützte Auth-Flows

- Email/Password
- Magic-Link
- Passwort-Reset
- Email-Verification
- (später: SSO für Enterprise-Kunden)

---

## 🚚 Migration Vercel/Supabase → Hetzner

Der Migrationspfad ist Teil des Designs. Ab Milestone 6 ist der Stack bereits Docker-ready.

### Schritt-für-Schritt (~1 Tag Arbeit)

```bash
# 1. Hetzner Cloud VM (CX22 oder größer)
# 2. Docker + docker-compose installieren
ssh root@hetzner
apt update && apt install -y docker.io docker-compose-plugin

# 3. Repository klonen
git clone https://github.com/cryptoclemens/geotherm.git
cd geotherm

# 4. .env.production mit eigenem Postgres setzen
cat > .env.production <<EOF
VITE_API_URL=https://api.geotherm.vencly.com
VITE_AUTH_URL=https://auth.geotherm.vencly.com
VITE_ANON_KEY=...  # Keycloak Public Key
DATABASE_URL=postgresql://geotherm:PASS@db:5432/geotherm
EOF

# 5. Stack starten
docker compose --env-file .env.production up -d

# 6. DNS umstellen (CNAME → Hetzner-IP)
# 7. Let's Encrypt via Traefik oder Caddy

# 8. Supabase-Daten migrieren
pg_dump $SUPABASE_CONN > /tmp/supabase.sql
psql $LOCAL_CONN < /tmp/supabase.sql

# 9. Frontend-Build updaten (neue ENV)
npm run build
docker compose restart web
```

Detaillierte Anleitung: siehe `docs/HETZNER_MIGRATION.md` *(wird in Milestone 6 erstellt)*

---

## 🧪 Testing

```bash
# Unit-Tests (Vitest)
npm test

# Watch-Mode
npm test -- --watch

# Coverage
npm test -- --coverage

# E2E-Tests (Playwright, ab M7)
npm run test:e2e
```

**Wichtige Test-Suites:**
- `src/apps/deltat/calc/system.test.js` – alle Formeln aus PLAUSI_CHECK.md
- `src/core/auth/useAuth.test.js` – Auth-Flows
- `src/core/router/router.test.js` – In-App-Registry

---

## 📝 Contributing

### Git Workflow

- `main` – produktiv, automatisch zu Vercel deployed
- `dev` – Integrationsbranch (optional)
- `feature/*` – Feature-Branches, PRs gegen `main`
- `fix/*` – Bugfix-Branches
- `docs/*` – reine Dokumentationsänderungen

### Commit-Style

Semantic Commits auf Deutsch:

```
feat: Auth-Flow für Magic-Link implementiert
fix: Dubletten-Dimensionierung berücksichtigt WP-Beitrag
docs: README Migration-Sektion ergänzt
refactor: ParamSlider in core/ui verschoben
test: Unit-Tests für calculateSystem() (20 Fälle)
chore: Tailwind auf 4.2 aktualisiert
```

### Code-Style

- 2 Spaces, Single-Quotes, Trailing-Commas
- Keine Semikolons am Zeilenende (ESLint-Regel)
- Komponenten als `function Comp()` (kein `const`)
- Hooks am Anfang, Handler in der Mitte, JSX am Ende

### Pull Requests

- Immer gegen `main` (außer bei Hotfixes, die direkt auf `main` gemerged werden dürfen)
- Beschreibung mit „Was" + „Warum"
- Screenshots bei UI-Änderungen
- Unit-Tests für neue Logik
- Keine PRs ohne grüne CI

---

## 📚 Wissenschaftliche Basis (für den Rechner-Kern)

Alle Formeln in der DeltaT-In-App basieren auf:

| Referenz | Anwendung |
|---|---|
| **VDI 4640** Blatt 1–4 | Thermische Nutzung des Untergrundes |
| **EN 14511** | Leistungsdefinition Wärmepumpen |
| **Drost (1978)** | Durchbruchszeit-Formel für Dubletten |
| **Arpagaus et al. (2018)** | Hochtemperatur-WP Klassifikation |
| **Zühlsdorf et al. (2019)** | Ultra-HT-WP / ORC Bereich |
| **DVGW W 115** | Werkstoffauswahl Thermalwasser |
| **IEA HPP Annex 35** | Reale COP vs. Carnot-COP |
| **VDI Wärmeatlas** (2019) | LMTD & U-Wert Plattenwärmetauscher |
| **DIN 4030** | Scaling-Bewertung |

Siehe `PLAUSI_CHECK.md` für den vollständigen Review mit allen Formeln, gefundenen Bugs und deren Behebung.

---

## ⚠ Disclaimer

Geotherm liefert **Vorauslegung auf Machbarkeitsebene**. Für konkrete Investitionsentscheidungen oder Genehmigungsverfahren sind erforderlich:

- Hydrogeologisches Gutachten mit Wasseranalyse
- 3D-Simulation der Wärmeausbreitung (FEFLOW, TOUGH2 oder COMSOL)
- Standortspezifische Untersuchungen
- Detaillierte Wirtschaftlichkeitsrechnung

Dieser Hinweis ist auf jeder Rechner-Seite im UI prominent sichtbar.

---

## 📄 Lizenz & Impressum

**vencly GmbH**
Leopoldstraße 31, 80802 München
HRB 290524 (AG München) · USt-ID: DE367131457
Vertretungsberechtigt: Clemens Eugen Theodor Pompeÿ
📧 [hello@vencly.com](mailto:hello@vencly.com) · 🌐 [www.vencly.com](https://www.vencly.com)

**Proprietäre Software.** Der Quellcode dieses Repositories ist Eigentum der vencly GmbH. Siehe `LICENSE`-Datei *(wird in M1 erstellt)* für Details zu Nutzungsrechten.

---

## 🔗 Verwandte Repositories

- **Vorgänger 1:** [cryptoclemens/vencly-delta-t](https://github.com/cryptoclemens/vencly-delta-t) — Single-File-HTML-Version des Rechners *(wird nach Migration archiviert)*
- **Vorgänger 2:** [cryptoclemens/geopotatlas](https://github.com/cryptoclemens/geopotatlas) — Standalone-Atlas mit Passwort-Gate *(wird nach Migration archiviert)*

---

Built with ♥ by [Vencly](https://www.vencly.com) using [Claude Code](https://claude.com/claude-code)
