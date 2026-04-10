# TASKS – DeltaT Geothermie-Auslegungsrechner

**Fortschritt:** ~90 % Alpha-Feature-Set abgeschlossen

---

## ✅ Abgeschlossen

### Berechnung & Wissenschaft
- [x] Primärkreis-Berechnung (ΔT, Q_th, Dubletten, Durchbruchszeit, d_opt)
- [x] Sekundärkreis (COP Carnot×50%, P_el WP, LMTD, Wärmetauscher-Fläche)
- [x] WP-Typ-Klassifikation nach ΔT_hub (VDI 4640, Arpagaus 2018, Zühlsdorf 2019)
- [x] Materialklassen nach TDS (VDI 2045, DVGW W 101)
- [x] Scaling-Risikoklassen nach TDS (DIN 4030)
- [x] Jahreswärmemenge [MWh/a] mit Laufstunden-Slider
- [x] Wissenschaftlicher Plausi-Check (4 kritische Bugs behoben)
- [x] Excel-Formelprüfungsdatei (21 Formeln + Literaturquellen)
- [x] **Bugfix Review 1**: Q_th-Formel ×1000
- [x] **Bugfix Review 1**: Tauchpumpen-Formel ×82
- [x] **Bugfix Review 1**: WP-Elektrik Q/(COP−1) statt Q/COP
- [x] **Bugfix Review 2**: Dubletten-Dimensionierung berücksichtigt WP-Beitrag
- [x] **Bugfix Review 2**: Thermik-Ampel vergleicht Q_delivered vs. Ziel
- [x] **Bugfix Review 2**: Default T_GW & Bohrloch-Abstand plausibilisiert

### UI & UX
- [x] 12 Eingabe-Slider mit logarithmischer Skalierung (k_f)
- [x] Info-Popups (ⓘ) für alle Parameter via ReactDOM.createPortal
- [x] Echtzeit-Berechnung ohne Submit-Button
- [x] Ampel-Statusleiste (Hydraulik, Thermik, Durchbruch, COP, Material)
- [x] Handlungsempfehlungs-Bar (priorisiert, mit Schnellkorrektur-Button)
- [x] Kompakter Primärkreis (3×3 KPI-Chips, ausklappbare Detailtabelle)
- [x] Leistungsvergleich-Balkendiagramm (Q_geo und Q_delivered separat)
- [x] Quellen-Popup (10 Literaturquellen)
- [x] Impressum-Popup (vollständig, vencly GmbH)
- [x] **Freie Texteingabe** für T_VL und T_RL ohne Slider-Begrenzung
- [x] **Drucken / PDF-Export** via Browser-Druckdialog (window.print + @media print)
- [x] **Mobile-Viewport** vollständig überarbeitet (2-spaltiges KPI-Grid, < 480 px)
- [x] **Versionsnummer automatisiert** (Format `v{Jahr}.W{KW}.{HHMM}`)

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
- [ ] **Experten-Feedback einarbeiten** – hydraulische Modellabgleiche für Bohrloch-Abstand (offen aus Gutachter-Review)

### Mittel (Beta-Features)
- [ ] **Geologische Karten-Layer** – Leaflet.js + GeoTIFF für standortbasierte Parameter-Vorbelegung
- [ ] **Mehrstandort-Vergleich** – parallele Parametersätze, Tabellenansicht
- [ ] **Strukturierter PDF-Export** – neben Browser-Druck auch programmatisches PDF mit Projektname und Datum
- [ ] **Saisonale Betrachtung** – Jahresganglinien (Jan–Dez) statt Dauerbetrieb

### Niedrig (Post-Beta)
- [ ] **Supabase Auth** – gespeicherte Konfigurationen pro Nutzer
- [ ] **Projekt-Management** – mehrere Projekte anlegen, benennen, exportieren
- [ ] **Einbettung als Widget** – iFrame-fähig für externe Websites
- [ ] **Englische Lokalisierung**
- [ ] **CSV-Export** der Eingabeparameter und Ergebnisse
- [ ] **Sensitivitätsanalyse** (Tornado-Diagramm)
- [ ] **Wirtschaftlichkeitsmodul** (CAPEX, OPEX, LCOH)

---

## 🔬 Wissenschaftliche Offene Punkte

- [ ] **Sättigungsindex (Langelier SI)** für präzisere Scaling-Bewertung
- [ ] **Kluftaquifer-Modus** – Durchbruchszeit nach Bear 1972 statt Drost 1978
- [ ] **Thermische Rückwirkung** – Aquifer-Abkühlung über Betriebszeit
- [ ] **Reale Pump-Kennlinien** statt pauschaler η = 0,60
- [ ] **Chlorid-basierte Materialklasse** statt TDS allein (DVGW W 115)
- [ ] **COP mit Evaporator-Pinch** (T_GW minus 3–5 K) statt T_GW direkt

---

## 🐛 Bekannte Einschränkungen

| Thema | Status | Details |
|---|---|---|
| Materialklasse nur TDS-basiert | ⚠ | Chloridgehalt wäre aussagekräftiger (DVGW W 115) |
| COP nutzt T_GW statt T_Verdampfer | ⚠ | Pinch ~3–5 K nicht berücksichtigt (optimistisch) |
| Drost-Formel homogen/isotrop | ⚠ | Reale Aquifere 30–50 % früher Durchbruch möglich |
| Tauchpumpen-Pumphöhe | ⚠ | Aktuell = Reservoirtiefe, ignoriert Drawdown |

Diese Vereinfachungen sind **für Machbarkeitsebene akzeptabel** und sollten bei der Interpretation berücksichtigt werden. Details in **[PLAUSI_CHECK.md](PLAUSI_CHECK.md)**.

---

## 📋 Technische Schulden

- [ ] **Single-File-Grenze** – `delta-t.html` nähert sich 2000 Zeilen; Migration zu Vite + Multi-File React evaluieren
- [ ] **Keine Unit-Tests** – `calculateSystem()` ist isolierbar und testbar
- [ ] **Babel-Transpilation im Browser** – für Produktion sollte pre-compiled gebaut werden
