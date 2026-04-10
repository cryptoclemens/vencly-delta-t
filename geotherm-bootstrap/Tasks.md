# Tasks – Geotherm by Vencly

**Legende:** ⭐ Blocker · 🔥 Muss · 📦 Soll · 💡 Kann · ⏸ Später

---

## Milestone 0 – Bootstrap *(diese Bootstrap-Session)*

- [x] ⭐ **Neues Repo** `github.com/cryptoclemens/geotherm` anlegen
- [x] ⭐ **BRIEF.md** – Produktvision, Zielgruppe, Scope
- [x] ⭐ **Tasks.md** – Dieser Backlog
- [x] ⭐ **README.md** – Setup & Architektur-Überblick
- [ ] ⭐ **Initial-Commit** auf `main` pushen

**Abschluss-Kriterium:** Drei Markdown-Dateien auf `main`, Repo ist lesbar und verständlich für neue Sitzung.

---

## Milestone 1 – Projekt-Setup & Shell *(Woche 1)*

### 1.1 Vite-Projekt initialisieren 🔥

- [ ] `npm create vite@latest geotherm -- --template react` als Basis
- [ ] React 19 + Vite 7 + Tailwind CSS 4 + Zustand + TanStack Query installieren
- [ ] ESLint + Prettier konfigurieren (Vencly-Style: 2 Spaces, Single Quotes, Trailing Commas)
- [ ] **Keine** hardcoded API-URLs – alle Env-Vars über `VITE_*` aus `.env.local` / Vercel Secrets

### 1.2 Projektstruktur 🔥

```
geotherm/
├── public/
│   ├── favicon.svg
│   └── vencly.png
├── src/
│   ├── apps/                # In-Apps (modular)
│   │   ├── gpa/             # Geothermie-Potenzial-Atlas
│   │   │   ├── components/
│   │   │   ├── store/
│   │   │   ├── data/
│   │   │   └── index.jsx    # Lazy-loaded Entry
│   │   └── deltat/          # DeltaT-Rechner
│   │       ├── components/
│   │       ├── store/
│   │       ├── calc/        # Pure Berechnungslogik
│   │       └── index.jsx
│   ├── core/                # Gemeinsame Infrastruktur
│   │   ├── auth/            # Auth-Adapter (Supabase → Keycloak-fähig)
│   │   ├── api/             # REST-Client
│   │   ├── ui/              # Shared Components (Button, Modal, Input…)
│   │   ├── layout/          # Shell, Header, Sidebar, Footer
│   │   └── router/          # React-Router-Konfig mit In-App-Registry
│   ├── pages/               # Legal + Marketing
│   │   ├── Landing.jsx
│   │   ├── Impressum.jsx
│   │   ├── Datenschutz.jsx
│   │   ├── AGB.jsx
│   │   └── Security.jsx
│   ├── hooks/
│   ├── store/               # Globaler Zustand (User, Theme, App-Registry)
│   ├── styles/
│   │   └── index.css        # Tailwind + CSS-Variables
│   ├── App.jsx
│   └── main.jsx
├── .env.example
├── Dockerfile               # für Hetzner-Migration
├── docker-compose.yml       # Postgres + App
├── vite.config.js
├── tailwind.config.js
├── package.json
├── README.md
├── BRIEF.md
├── Tasks.md
├── CLAUDE.md                # Claude-Code-Konventionen
└── CHANGELOG.md
```

### 1.3 Router & App-Registry 🔥

- [ ] React Router v6 installieren (Hash-Router für statisches Hosting)
- [ ] **App-Registry-Pattern**: `src/core/router/apps.js` definiert alle In-Apps
  ```js
  export const APPS = [
    { key: 'gpa',    name: 'Geothermie-Potenzial-Atlas', path: '/atlas',  icon: '🗺',  load: () => import('../../apps/gpa') },
    { key: 'deltat', name: 'DeltaT Auslegungsrechner',   path: '/deltat', icon: '🧮', load: () => import('../../apps/deltat') },
  ]
  ```
- [ ] Lazy-Loading jeder In-App via `React.lazy()`
- [ ] Fallback-Route `/` → Landing-Page
- [ ] 404-Route

### 1.4 Core-Layout 🔥

- [ ] **Header**: Vencly-Logo links, In-App-Switcher mittig, User-Menü rechts
- [ ] **Footer**: Impressum · Datenschutz · AGB · Security-Links + Version-Badge
- [ ] **Shell-Layout** mit `<Outlet />` für In-Apps
- [ ] **Design-Tokens** in Tailwind-Theme (Vencly-Farben: navy `#0D2B55`, primary `#2E75B6`, etc.)
- [ ] Responsive (Mobile-first)

### 1.5 Landing-Page 📦

- [ ] Hero-Sektion mit Claim „Vom Standort zum Bohrplan"
- [ ] In-App-Übersicht als Cards mit Screenshots
- [ ] Feature-Highlights (modular, DSGVO, wissenschaftlich validiert)
- [ ] Call-to-Action: „Kostenlos registrieren" / „Live-Demo ohne Login"
- [ ] Footer mit Legal-Links

**Abschluss-Kriterium M1:** `npm run dev` läuft, Landing zeigt zwei In-App-Cards, Navigation zu leeren In-App-Stubs funktioniert.

---

## Milestone 2 – Auth & Nutzer-Konto *(Woche 2)*

### 2.1 Supabase Auth-Backend ⭐

- [ ] Supabase-Projekt `geotherm-production` anlegen (EU-Frankfurt)
- [ ] **Eigenes** Projekt, getrennt von DeltaT-Alt und Geopotatlas-Alt
- [ ] Email/Password + Magic-Link aktivieren
- [ ] E-Mail-Templates in Deutsch anpassen (Vencly-Branding)

### 2.2 Auth-Adapter (Migrations-sicher) 🔥

- [ ] `src/core/auth/useAuth.js` – **eigener Hook** als einziger Zugriffspunkt
  - `signIn(email, password)` / `signUp(…)` / `signOut()` / `user` / `loading`
  - Intern nutzt er `@supabase/supabase-js` — später austauschbar gegen REST-Client
- [ ] `AuthProvider` in `src/core/auth/AuthProvider.jsx`
- [ ] **Niemals** `supabase.auth.*` direkt in Komponenten aufrufen — immer über `useAuth()`

### 2.3 Auth-UI 🔥

- [ ] `/login` – E-Mail + Passwort + „Angemeldet bleiben"
- [ ] `/signup` – E-Mail + Passwort + Bestätigungs-Checkboxen (AGB, Datenschutz)
- [ ] `/forgot-password` – Magic-Link
- [ ] `/verify-email` – Bestätigungsseite
- [ ] Protected Routes via `<RequireAuth>`-Wrapper
- [ ] User-Menü im Header mit Profil, Einstellungen, Logout

### 2.4 User-Profil-Tabelle (Supabase) 🔥

```sql
CREATE TABLE profiles (
  id           UUID PRIMARY KEY REFERENCES auth.users(id),
  email        TEXT UNIQUE NOT NULL,
  full_name    TEXT,
  company      TEXT,
  role         TEXT DEFAULT 'user',  -- user | admin
  tier         TEXT DEFAULT 'free',  -- free | pro | team | enterprise
  created_at   TIMESTAMPTZ DEFAULT NOW(),
  updated_at   TIMESTAMPTZ DEFAULT NOW()
);
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users can read own profile" ON profiles FOR SELECT USING (auth.uid() = id);
CREATE POLICY "Users can update own profile" ON profiles FOR UPDATE USING (auth.uid() = id);
```

### 2.5 Legal-Seiten ⭐

- [ ] `/impressum` – aus DeltaT übernehmen, aktualisieren
- [ ] `/datenschutz` – DSGVO-konform, erwähne Supabase, localStorage, optional Analytics
- [ ] `/agb` – Nutzungsbedingungen für Free-Tier + Hinweis auf separate Enterprise-Verträge
- [ ] `/security` – Datensicherheits-Übersicht (Verschlüsselung, Hosting, Backup-Strategie)
- [ ] Links im Footer + Signup-Checkboxen

**Abschluss-Kriterium M2:** Nutzer kann sich registrieren, einloggen, ausloggen, Passwort zurücksetzen. Profile-Tabelle funktioniert mit RLS.

---

## Milestone 3 – GPA Migration *(Woche 3)*

### 3.1 Code-Migration aus geopotatlas-Repo 🔥

- [ ] `src/components` → `src/apps/gpa/components`
- [ ] `src/store` → `src/apps/gpa/store` (Prefix `gpa_*` für Store-Keys)
- [ ] `src/data` → `src/apps/gpa/data`
- [ ] Abhängigkeiten in main `package.json` konsolidieren (react-leaflet, leaflet)
- [ ] **Passwort-Gate entfernen** – ersetzt durch echten Auth

### 3.2 GPA in App-Shell integrieren 🔥

- [ ] Als Route `/atlas` registrieren
- [ ] Header/Footer der App-Shell nutzen (nicht eigener `<header>`)
- [ ] Vencly-Design-Tokens auf GPA-Komponenten anwenden
- [ ] Print-Dialog & Feedback-Modal in Core-UI verschieben (shared)

### 3.3 Feature-Kompatibilität verifizieren 📦

- [ ] WMS-Layer laden
- [ ] FW-Städte-Marker funktionieren
- [ ] OSM-Heat-Sources laden
- [ ] Legend + InfoPanel funktionieren
- [ ] Guided Tour läuft

**Abschluss-Kriterium M3:** GPA läuft als `/atlas` innerhalb der neuen Shell, login-geschützt, mit allen Features der Standalone-Version.

---

## Milestone 4 – DeltaT Migration *(Woche 4)*

### 4.1 Single-File → Multi-File React 🔥

- [ ] `calculateSystem()` aus `delta-t.html` extrahieren → `src/apps/deltat/calc/system.js`
- [ ] **Unit-Tests** dafür (Vitest) — 20+ Testfälle auf Basis PLAUSI_CHECK.md
  - Thermische Leistung, Tauchpumpe, COP, WP-Elektrik, LMTD, Drost
  - Edge Cases: T_GW < T_VL, deltaT ≤ 0, COP → ∞
- [ ] Komponenten auseinanderziehen:
  - `ParamSlider` → `src/core/ui/ParamSlider.jsx` (shared!)
  - `KpiTile`, `BarChart` → `src/apps/deltat/components/`
  - `InputColumn`, `PrimaryColumn`, `SecondaryColumn` → `src/apps/deltat/components/`
  - `InfoPopup`, `QuellenModal`, `ImpressumModal` → löschen (gibt's jetzt in der Shell)
- [ ] State in Zustand-Store: `src/apps/deltat/store/useDeltaTStore.js`
- [ ] Tailwind statt inline CSS-Variablen

### 4.2 Alle Bugfixes aus Review 1+2 mitnehmen 🔥

- [x] Q_th-Formel ohne ×1000
- [x] Tauchpumpe mit ρ×g×H/η
- [x] WP-Elektrik Q/(COP−1)
- [x] Dubletten-Anzahl mit WP-Beitrag (`Q_geo_benötigt = Q_Ziel × (COP-1)/COP`)
- [x] Thermik-Ampel vergleicht Q_delivered vs. Ziel
- [x] Default T_GW=25, abstand=500
- [x] Freie Texteingabe T_VL / T_RL

### 4.3 DeltaT in App-Shell integrieren 🔥

- [ ] Als Route `/deltat` registrieren
- [ ] Header/Footer aus Shell
- [ ] Print-CSS für PDF-Export portieren
- [ ] Feedback-Modal nutzt Core-Version

**Abschluss-Kriterium M4:** DeltaT läuft als `/deltat`, alle 4 behobenen Bugs sind verifiziert durch Unit-Tests, UI entspricht Vencly-Design.

---

## Milestone 5 – Daten-Austausch zwischen Apps *(Woche 5)*

### 5.1 Shared Workspace State 🔥

- [ ] `src/store/useWorkspaceStore.js` – globaler Store für geteilte Daten
- [ ] Datenmodell `LocationPreset`:
  ```ts
  {
    id: string
    name: string
    source: 'gpa' | 'manual'
    lat: number
    lng: number
    geology: { tiefe, maechtig, kf, tGW, tds }
    created_at: string
  }
  ```
- [ ] Persistierung in `localStorage` (kein Login nötig) + optional Supabase (mit Login)

### 5.2 GPA → DeltaT 🔥

- [ ] Popup auf Karte: Button **„In DeltaT öffnen"**
- [ ] Klick erzeugt `LocationPreset` aus Marker-Daten + navigiert zu `/deltat?preset={id}`
- [ ] DeltaT liest Query-Param und füllt Inputs vor
- [ ] Info-Banner: „Werte vorbefüllt aus Standort: XYZ" mit „Preset entfernen"-Button

### 5.3 DeltaT → GPA 📦

- [ ] DeltaT hat Button **„Passende Standorte finden"**
- [ ] Navigiert zu `/atlas?filter={tGW_min,tGW_max,maechtig_min,…}`
- [ ] GPA filtert Marker entsprechend

### 5.4 Projekte speichern 📦 *(nur eingeloggt)*

```sql
CREATE TABLE projects (
  id           UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id      UUID NOT NULL REFERENCES profiles(id),
  name         TEXT NOT NULL,
  description  TEXT,
  location     JSONB,   -- LocationPreset
  deltat_input JSONB,   -- DeltaT-Parameter
  deltat_result JSONB,  -- berechnete Outputs (Cache)
  created_at   TIMESTAMPTZ DEFAULT NOW(),
  updated_at   TIMESTAMPTZ DEFAULT NOW()
);
ALTER TABLE projects ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users read own projects" ON projects FOR SELECT USING (auth.uid() = user_id);
CREATE POLICY "Users insert own projects" ON projects FOR INSERT WITH CHECK (auth.uid() = user_id);
CREATE POLICY "Users update own projects" ON projects FOR UPDATE USING (auth.uid() = user_id);
CREATE POLICY "Users delete own projects" ON projects FOR DELETE USING (auth.uid() = user_id);
```

- [ ] `/projects` – Übersichtsseite mit gespeicherten Projekten
- [ ] „Speichern"-Button in DeltaT (wenn eingeloggt)
- [ ] „Laden"-Button in DeltaT

**Abschluss-Kriterium M5:** Nutzer kann in GPA auf Marker klicken → in DeltaT öffnen → Ergebnis speichern → in der Projekt-Liste wiederfinden.

---

## Milestone 6 – Deployment & Domain *(Woche 6)*

### 6.1 Vercel-Deployment ⭐

- [ ] Vercel-Projekt verknüpft mit GitHub-Repo
- [ ] Env-Vars: `VITE_SUPABASE_URL`, `VITE_SUPABASE_ANON_KEY`, `VITE_SENTRY_DSN` (optional)
- [ ] Preview-Deployments für jeden Branch
- [ ] Production-Deploy von `main`

### 6.2 Domain `geotherm.vencly.com` ⭐

- [ ] DNS-CNAME bei Vencly-DNS-Provider setzen
- [ ] SSL über Vercel (automatisch)
- [ ] Redirect `geotherm.vencly.de` → `.com`?

### 6.3 Hetzner-Migration vorbereiten 📦

- [ ] `Dockerfile` – Multi-Stage-Build (Node-Build → Nginx-Serve)
- [ ] `docker-compose.yml` – App + Postgres + Backup-Volume
- [ ] `nginx.conf` – SPA-Fallback, Brotli, Security-Headers
- [ ] **Deployment-Doku** in `README.md` – Schritt-für-Schritt Hetzner-Setup
- [ ] Backup-Skripte für Postgres-Dumps nach S3/Hetzner-Storage

### 6.4 Monitoring & Analytics 💡

- [ ] Sentry für Error-Tracking (optional, mit DSGVO-Opt-in)
- [ ] Plausible Analytics (DSGVO-freundlich, selbst-hostbar)
- [ ] Uptime-Check (z.B. UptimeRobot)

**Abschluss-Kriterium M6:** `geotherm.vencly.com` ist live, SSL gültig, Monitoring läuft, Hetzner-Migration ist dokumentiert aber noch nicht ausgeführt.

---

## Milestone 7 – Polish & Launch *(Woche 7)*

- [ ] 📦 Onboarding-Tour beim ersten Login
- [ ] 📦 Notification-System (z.B. „Du hast ein neues Projekt gespeichert")
- [ ] 📦 Export-Funktionen (CSV, PDF) für Projekte
- [ ] 📦 Accessibility-Audit (WCAG 2.1 AA)
- [ ] 📦 Performance-Audit (Lighthouse > 90)
- [ ] 📦 Mobile-Testing (iOS Safari, Android Chrome)
- [ ] 📦 Cross-Browser-Test (Firefox, Safari, Edge)
- [ ] 📦 Public Beta Announcement auf LinkedIn + Branchen-Newsletter
- [ ] 💡 Feedback aus Beta einarbeiten

**Abschluss-Kriterium M7:** Geotherm ist als öffentliche Beta live, erste echte Nutzer sind eingeladen.

---

## Backlog – Weitere In-Apps *(ab Milestone 8+)*

- [ ] ⏸ **Bohrkosten-Kalkulator** (CAPEX/OPEX pro Projekt)
- [ ] ⏸ **LCOH-Modul** (Levelized Cost of Heat über 25 Jahre)
- [ ] ⏸ **Genehmigungs-Guide** (WHG-Anträge pro Bundesland)
- [ ] ⏸ **Netzanschluss-Planer** (Distanz zu Fernwärme)
- [ ] ⏸ **CO₂-Einsparungs-Report**

Jede neue In-App folgt dem gleichen Schema: eigener Ordner `src/apps/{name}`, Registrierung in `src/core/router/apps.js`, eigene Unit-Tests.

---

## Technische Schulden & Aufräumarbeiten

- [ ] ⏸ Alte `vencly-delta-t`-Repo als Archiv markieren (Read-Only)
- [ ] ⏸ Alte `geopotatlas`-Repo als Archiv markieren
- [ ] ⏸ Redirects von alten URLs auf neue (`delta-t.html` → `/deltat`)
- [ ] ⏸ Migration der Supabase-Feedback-Daten aus beiden alten Projekten
- [ ] ⏸ Archivierung der ursprünglichen Formelprüfungs-Excel in Geotherm-Docs

---

## Offene strategische Entscheidungen

Vor Milestone 1 entscheiden:

1. **Auth-Anbieter final**: Supabase (schnell) vs. Keycloak (self-hosted von Anfang an)?
2. **Open-Source-Kernel**: Wird der Kern-Shell-Code MIT-lizenziert?
3. **Pricing-Modell**: Monatlich oder jährlich abrechnen? Welches Pricing-Tool? (Stripe?)
4. **i18n-Strategie**: Ab Launch Englisch mit dabei oder später?
5. **Analytics**: Plausible reicht oder zusätzlich PostHog für Product-Analytics?

Siehe **BRIEF.md → Abschnitt 8** für weitere offene Fragen.

---

## Definition of Done (pro Milestone)

Ein Milestone gilt als „done", wenn:

1. Alle ⭐/🔥-Tasks erledigt sind
2. Es gibt Unit-Tests für neue Logik (wo sinnvoll)
3. Die Code-Änderungen sind auf `main` gemerged
4. README.md ist aktualisiert
5. Das Vercel-Preview-Deployment läuft grün
6. Ein kurzer Walk-through-Screenshot / -Video wurde im Slack / per Mail geteilt
