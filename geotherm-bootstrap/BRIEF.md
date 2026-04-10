# BRIEF – Geotherm by Vencly

**Stand:** April 2026
**Repository:** [github.com/cryptoclemens/geotherm](https://github.com/cryptoclemens/geotherm)
**Zieldomain:** [geotherm.vencly.com](https://geotherm.vencly.com)

---

## 1. Vision

**Geotherm** ist die erste modulare **Geothermie-Suite im Web** — eine einheitliche Plattform, die alle Werkzeuge entlang des Geothermie-Projektlebenszyklus bündelt:

> **Vom Standort zum Bohrplan in einem Workflow.**

Statt isolierter Tools, bei denen Planer zwischen Excel-Tabellen, QGIS, händischen Berechnungen und PDF-Gutachten hin- und herspringen, bietet Geotherm einen **durchgängigen digitalen Workflow** mit geteilten Daten und einem gemeinsamen Nutzerkonto.

---

## 2. Das Produkt

### 2.1 Modulare Architektur

Geotherm ist keine monolithische App, sondern ein **App-Container**. Jede Teil-App (im Folgenden *In-App* genannt) ist ein eigenständiges Feature-Modul mit klaren Grenzen:

- **Eigener Route-Slot** (`/atlas`, `/deltat`, …)
- **Eigener State-Slice** (Zustand-Store pro App)
- **Eigene DB-Schema-Namespaces** (`gpa_*`, `deltat_*`, …)
- **Eigenes Go-to-Market**: Jede In-App kann als **Standalone-Produkt** herausgeschnitten und an einen einzelnen Kunden lizenziert werden

### 2.2 In-Apps zum Launch

| Code | Name | Beschreibung |
|---|---|---|
| **GPA** | Geothermie-Potenzial-Atlas | Interaktive Karte des norddeutschen Tieflandes mit Overlay für Lockergestein, Fernwärme-Städte, Industriewärmequellen und Rechenzentren |
| **DeltaT** | Dubletten-Auslegungsrechner | Echtzeit-Rechner für geothermische Dubletten-Systeme (Förder- und Reinjektionsbohrung) mit WP-Dimensionierung |

### 2.3 Daten-Austausch zwischen Apps

Kern der Plattform: Die In-Apps können **Daten austauschen**, bleiben dabei aber lose gekoppelt:

**Beispiel-Workflow „Atlas → Rechner":**
1. Nutzer sucht in **GPA** nach vielversprechenden Standorten
2. Klickt auf einen Marker → Popup mit geologischen Kennwerten (T_GW, Mächtigkeit, k_f)
3. Button **„In DeltaT öffnen"** → Parameter werden in DeltaT vorbefüllt, dort wird die Auslegung berechnet
4. Ergebnis wird als *Projekt* gespeichert (verknüpft mit dem ursprünglichen Standort)

**Beispiel-Workflow „Rechner → Atlas":**
1. Nutzer rechnet in **DeltaT** eine fiktive Dublette durch
2. Klickt **„Wo gibt's solche Bedingungen?"** → GPA filtert die Karte auf Standorte, die zu den Eingangsparametern passen

### 2.4 Roadmap für weitere In-Apps (ab Q3)

- **Bohrkosten-Kalkulator** – CAPEX/OPEX pro Projekt
- **Wirtschaftlichkeit (LCOH)** – Levelized Cost of Heat über 25 Jahre
- **Genehmigungs-Guide** – Bundesland-spezifische Checklisten für WHG-Anträge
- **Netzanschluss-Planer** – Distanz zu nächstem Fernwärmenetz + Anschlusskosten
- **CO₂-Einsparungs-Report** – Vergleich gegen Gas/Öl-Heizlösungen

---

## 3. Zielgruppen

### 3.1 Primäre Zielgruppe (Jahr 1)

**Geothermie-Projektentwickler** (5–50 MA):
- Brauchen schnelle Vorabschätzungen für Akquise
- Jonglieren zwischen Excel, QGIS und PowerPoint
- Zahlungsbereit: 500–2.000 €/Jahr pro Lizenz

**Energieversorger & Stadtwerke** (Fernwärme-Betreiber):
- Evaluieren Dekarbonisierungs-Strategien für ihre Wärmenetze
- Brauchen belastbare Zahlen für Vorstandspräsentationen
- Zahlungsbereit: 5.000–20.000 €/Jahr (Enterprise-Lizenz)

### 3.2 Sekundäre Zielgruppen

**Ingenieurbüros & Gutachter:**
- Nutzen Geotherm für Sanity-Checks ihrer eigenen FEFLOW-Simulationen
- Vertrieb über Partnerschaften / Whitelabel

**Kommunen & Landkreise:**
- Bedarfsanalyse für kommunale Wärmepläne (§ 4 WPG)
- Einstiegs-Targeting über Verbände & Cluster

**Forschung & Lehre:**
- Kostenlose Ausbildungslizenzen für Unis

### 3.3 Nicht-Zielgruppen

- ❌ Endkunden / Privathaushalte
- ❌ Internationale Nutzer ohne DACH-Bezug *(Jahr 1)*
- ❌ Tiefengeothermie > 2.500 m *(aktuell nicht im Scope)*

---

## 4. Positionierung

### 4.1 Wettbewerbs-Landkarte

| Tool | Stärke | Schwäche vs. Geotherm |
|---|---|---|
| **FEFLOW, TOUGH2, COMSOL** | Industriestandard für 3D-Simulation | Teuer, Ingenieur-only, keine Karten-Integration |
| **Excel-Vorlagen (diverse)** | Vertraut, offline nutzbar | Keine Echtzeit, kein Teamwork, fehleranfällig |
| **QGIS + BGR-WMS** | Kostenlos, mächtig | Steile Lernkurve, keine Berechnung |
| **BGR Geothermis-Portal** | Offizielle Datenquelle | Nur Viewer, keine Projektplanung |
| **Geothermie.ch (CH)** | Ähnlicher Ansatz für CH | Nur CH, kein Rechner |

**Unser USP:** *Der einzige Webservice, der Kartendaten, Auslegungsrechner und Wirtschaftlichkeit in einem nahtlosen Workflow verbindet — ohne Installation, mit deutschem Fokus und wissenschaftlich validierten Formeln.*

### 4.2 Preismodell (Hypothese)

| Tier | Preis | Features |
|---|---|---|
| **Free** | 0 € | Atlas-Viewer, DeltaT eingeschränkt (keine Speicherung), DeltaT-PDF mit Wasserzeichen |
| **Pro** | 49 €/Mo | Alle In-Apps, unbegrenzte Projekte, PDF-Export, Multi-Device |
| **Team** | 149 €/Mo | Pro + Teamfreigabe, Team-Workspaces, Billing |
| **Enterprise** | ab 5.000 €/Jahr | SLA, On-Prem-Deployment auf Hetzner, Whitelabel, Custom In-Apps |
| **Standalone-Lizenz** | Verhandlung | Eine einzelne In-App als eigenständige White-Label-Instanz |

---

## 5. Technische Prinzipien

### 5.1 Leitplanken

1. **Cloud-agnostisch**: Kein Vendor-Lock-in. Jede In-App muss auch auf einer einzelnen Hetzner-VM laufen können (Migration Vercel/Supabase → Hetzner darf max. 1 Woche Arbeit sein).
2. **Standalone-fähig**: Jede In-App kann als eigenständiges Repository extrahiert und an einen einzelnen Kunden ausgeliefert werden.
3. **Wissenschaftlich validiert**: Alle Berechnungsformeln haben Quellenangaben (VDI, DIN, EN, peer-reviewed Paper). Keine Annahmen ohne Beleg.
4. **Datenschutz by Design**: DSGVO-konform. Keine Tracking-Cookies. Daten bleiben in der EU. Lokale Speicherung bevorzugt (localStorage + optional Cloud-Sync).
5. **Echtzeit-UX**: Kein „Submit"-Button. Jede Eingabe aktualisiert alle abhängigen Werte sofort.
6. **Mobile-first, Desktop-first**: Funktioniert auf beiden gleich gut — aber der Haupt-Use-Case ist Desktop (Planungsarbeit).
7. **Accessibility**: WCAG 2.1 AA als Minimum.

### 5.2 Tech-Stack

- **Frontend:** React 19 + Vite + Tailwind CSS 4 + Zustand + TanStack Query
- **Routing:** React Router v6 (Hash-Router für statisches Hosting)
- **Maps:** Leaflet + react-leaflet *(aus GPA)*
- **Auth & DB:** Supabase (für schnellen Start) — mit klarem Migrationspfad zu eigenem Postgres + Keycloak auf Hetzner
- **Deployment:** Vercel (Start) → Hetzner VM mit Docker Compose (Ziel)
- **Monitoring:** Sentry (optional) + Plausible Analytics (DSGVO-konform)

### 5.3 Migrationspfad Vercel/Supabase → Hetzner

Bereits **ab Tag 1** wird so gebaut, dass eine Migration ohne Code-Änderung funktioniert:

1. **Env-basierte Config**: Keine hardcoded Supabase-URLs. Alle Backend-URLs kommen aus `VITE_API_URL` / `VITE_AUTH_URL`.
2. **Eigener Auth-Adapter**: Statt `supabase.auth.signIn()` direkt → eigener `useAuth()`-Hook, der intern Supabase nutzt, später aber auf Keycloak umgestellt werden kann.
3. **REST-only**: Keine Supabase-spezifischen Features wie Realtime-Subscriptions oder Storage-Buckets. Alles über einfache REST-Calls, die auch gegen ein eigenes Express/Fastify-Backend funktionieren.
4. **DB-Schema portabel**: Reines Postgres, keine Supabase-Extensions. RLS-Policies werden später durch Backend-Autorisierung ersetzt.
5. **Deployment-Target-Abstraktion**: `Dockerfile` + `docker-compose.yml` liegen von Anfang an im Repo. Vercel baut das nicht, aber Hetzner kann es direkt ausführen.

---

## 6. Rechtliche Rahmenbedingungen

### 6.1 Betreiber

**vencly GmbH**
Leopoldstraße 31, 80802 München
HRB 290524 (AG München)
USt-ID: DE367131457
Vertretungsberechtigt: Clemens Eugen Theodor Pompeÿ
Kontakt: hello@vencly.com

### 6.2 Compliance-Anforderungen

- **DSGVO**: Vollständige Datenschutzerklärung mit Auskunftsrecht, Löschrecht, Portabilität
- **TMG § 5**: Impressum mit allen Pflichtangaben
- **AGB**: Nutzungsbedingungen für kostenpflichtige Lizenzen
- **Haftungsausschluss**: Explizit, dass Geotherm eine *Vorauslegung* liefert und keine detaillierte Planung ersetzt
- **Cookie-Banner**: DSGVO-konformer Opt-In (Essential-only ohne Banner; Analytics erst nach Zustimmung)
- **Impressum, Datenschutz, AGB, Datensicherheit** als eigene Routen (`/impressum`, `/datenschutz`, `/agb`, `/security`)

### 6.3 Wissenschaftlicher Disclaimer

Alle Berechnungen in Geotherm sind für die **Machbarkeitsebene (Feasibility Level)** geeignet. Für Investitionsentscheidungen, Genehmigungsverfahren oder Bohrplanung sind zusätzlich erforderlich:

- Hydrogeologisches Gutachten mit vollständiger Wasseranalyse (Cl⁻, H₂S, pH, T)
- 3D-Simulation der Wärmeausbreitung (FEFLOW, TOUGH2 oder COMSOL)
- Standortspezifische Untersuchungen zu Aquifer-Heterogenität
- Detaillierte Wirtschaftlichkeitsberechnung unter Berücksichtigung lokaler Strompreise, Förderungen und Umweltauflagen

Dieser Disclaimer muss auf jeder Rechner-Seite prominent sichtbar sein.

---

## 7. Erfolgskriterien (6 Monate nach Launch)

### Must-have (Q2 2026)

- [ ] 1.000 registrierte Nutzer (Free)
- [ ] 50 zahlende Pro-Kunden
- [ ] 5 Enterprise-Leads im Sales-Funnel
- [ ] 99.5 % Uptime
- [ ] < 2 s Time-to-Interactive auf 3G-Mobile
- [ ] 0 DSGVO-Beschwerden / Abmahnungen

### Nice-to-have (Q3–Q4 2026)

- [ ] 3 zahlende Enterprise-Kunden
- [ ] 1 verkaufte Standalone-Lizenz (Whitelabel)
- [ ] 3. In-App (Bohrkosten oder LCOH) live
- [ ] Integration mit einem GIS-Tool (QGIS-Plugin oder Export)
- [ ] Erste Press-Coverage in Branchenmagazin (Geothermie-Energie, Stadtwerke-Magazin)

---

## 8. Offene strategische Fragen

1. **Open-Source-Kernel oder Closed?** GPA + DeltaT könnten unter dualer Lizenz stehen (Community + Commercial) — macht Akquise einfacher, aber verkompliziert das Modell.
2. **Whitelabel-Pricing**: Einmalzahlung oder Revenue-Share mit Reseller?
3. **Datenpartnerschaften**: BGR, Landesämter, Energieagenturen — eigene API-Zugänge aushandeln?
4. **Internationalisierung**: Wann Schweiz und Österreich dazunehmen? (gleiche Normen, andere Geologie)
5. **Multi-Tenant-Architektur**: Jeder Enterprise-Kunde auf eigener Hetzner-Instanz oder shared Postgres mit RLS?

---

## 9. Historie

| Datum | Meilenstein |
|---|---|
| März 2026 | DeltaT Alpha live auf GitHub Pages |
| März 2026 | Geopotatlas internes Tool live |
| April 2026 | **Dieser Brief** — Entscheidung zur Verschmelzung in Geotherm-Suite |
| *geplant Q2 2026* | Geotherm Beta mit GPA + DeltaT + Auth |
| *geplant Q3 2026* | Geotherm Public Launch mit Pro-Tier |
| *geplant Q4 2026* | 3. In-App live, erste Enterprise-Kunden |
