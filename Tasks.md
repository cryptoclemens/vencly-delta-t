# TASKS – DeltaT Geothermie-Auslegungsrechner

**Fortschritt:** ~90% Alpha-Feature-Set abgeschlossen

---

## ✅ Abgeschlossen

### Berechnung & Wissenschaft
- [x] Primärkreis-Berechnung (ΔT, Q_th, Dubletten, Durchbruchszeit, d_opt)
- [x] Sekundärkreis (COP Carnot×50%, P_el WP, LMTD, Wärmetauscher-Fläche)
- [x] WP-Typ-Klassifikation nach ΔT_hub (VDI 4640, Arpagaus 2018, Zühlsdorf 2019)
- [x] Materialklassen nach TDS (VDI 2045, DVGW W 101)
- [x] Scaling-Risikoklassen nach TDS (DIN 4030)
- [x] Jahreswärmemenge [MWh/a] mit Laufstunden-Slider
- [x] Wissenschaftlicher Plausi-Check (3 kritische Bugs behoben)
- [x] Excel-Formelprüfungsdatei (21 Formeln + Literaturquellen)

### UI & UX
- [x] 12 Eingabe-Slider mit logarithmischer Skalierung (k_f)
- [x] Info-Popups (ⓘ) für alle Parameter via ReactDOM.createPortal
- [x] Echtzeit-Berechnung ohne Submit-Button
- [x] Ampel-Statusleiste (Hydraulik, Thermik, Durchbruch, COP, Material)
- [x] Handlungsempfehlungs-Bar (priorisiert, mit Schnellkorrektur-Button)
- [x] Kompakter Primärkreis (3×3 KPI-Chips, ausklappbare Detailtabelle)
- [x] Leistungsvergleich-Balkendiagramm
- [x] Quellen-Popup (10 Literaturquellen)
- [x] Impressum-Popup (vollständig, vencly GmbH)

### Infrastruktur
- [x] GitHub Pages Deployment (GitHub Actions)
- [x] Supabase Feedback-Integration (SDK v2, JWT-Key)
- [x] Cookie-Consent-Banner (DSGVO)
- [x] localStorage-Persistenz der Eingabewerte (bei Consent)
- [x] Vencly-Favicon (PNG, programmatisch generiert)
- [x] config.js / config.example.js Pattern (Credentials gitignored)

---

## 🔲 Offen – Priorisiert

### Hoch (Alpha → Beta)
- [x] **Versionsnummer automatisieren** – Format `v2026.W14.HHMM` wird zur Laufzeit aus `new Date()` berechnet (ISO-Woche + HHMM), kein manuelles Pflegen mehr nötig
- [ ] **Experten-Feedback einarbeiten** – hydraulische Modellabgleiche für Bohrloch-Abstand (offen aus Gutachter-Review)
- [x] **Mobile-Viewport** – vollständig überarbeitet: `html/body` scroll freigeschaltet, `#app-shell` entfixed, Spalten gestapelt, KPI-Grid 2-spaltig, sehr enge Phones (< 480 px) zusätzlich angepasst

### Mittel (Beta-Features)
- [ ] **Geologische Karten-Layer** – Leaflet.js + GeoTIFF für standortbasierte Parameter-Vorbelegung
- [ ] **Mehrstandort-Vergleich** – parallele Parametersätze, Tabellenansicht
- [ ] **Druckansicht / PDF-Export** – strukturiertes PDF mit Projektname und Datum
- [ ] **Saisonale Betrachtung** – Jahresganglinien (Jan–Dez) statt Dauerbetrieb

### Niedrig (Post-Beta)
- [ ] **Supabase Auth** – gespeicherte Konfigurationen pro Nutzer
- [ ] **Projekt-Management** – mehrere Projekte anlegen, benennen, exportieren
- [ ] **Einbettung als Widget** – iFrame-fähig für externe Websites
- [ ] **Englische Lokalisierung**

---

## 🔬 Wissenschaftliche Offene Punkte

- [ ] **Sättigungsindex (Langelier SI)** für präzisere Scaling-Bewertung
- [ ] **Kluftaquifer-Modus** – Durchbruchszeit nach Bear 1972 statt Drost 1978
- [ ] **Thermische Rückwirkung** – Aquifer-Abkühlung über Betriebszeit
- [ ] **Reale Pump-Kennlinien** statt pauschaler η = 0,60

---

## 📋 Technische Schulden

- [ ] **Single-File-Grenze** – `delta-t.html` nähert sich 2000 Zeilen; Migration zu Vite + Multi-File React evaluieren
- [ ] **Keine Unit-Tests** – `calculateSystem()` ist isolierbar und testbar
- [ ] **Babel-Transpilation im Browser** – für Produktion sollte pre-compiled gebaut werden
