# DeltaT – Geothermischer Dubletten-Auslegungsrechner

![Status](https://img.shields.io/badge/status-Alpha-yellow) ![License](https://img.shields.io/badge/license-proprietary-blue) ![Stack](https://img.shields.io/badge/stack-React%20%7C%20Supabase-2980B9)

**Live:** [cryptoclemens.github.io/vencly-delta-t/delta-t.html](https://cryptoclemens.github.io/vencly-delta-t/delta-t.html)

**DeltaT** ist ein interaktiver Echtzeit-Rechner zur **Vorauslegung geothermischer Dubletten-Systeme** (Förder- und Reinjektionsbohrung). Entwickelt von der [vencly GmbH](https://www.vencly.com) für eine schnelle Machbarkeitsprüfung von Geothermie-Projekten — vom Einfamilienhaus bis zum Stadtquartier.

> **Kein Build-System, keine Abhängigkeiten:** Eine einzige HTML-Datei, die du direkt im Browser öffnen kannst.

---

## ✨ Features

### Berechnungen (wissenschaftlich validiert)
- **Primärkreis-Auslegung**
  - Thermische Leistung pro Dublette: `Q_th = Q × ΔT × c_p` (VDI 4640)
  - Anzahl benötigter Dubletten mit **WP-Beitrags-Berücksichtigung**
  - Jahreswärmemenge [MWh/a] mit konfigurierbaren Laufstunden
  - Durchbruchszeit nach **Drost (1978)**: `t = π·n·b·d² / (4Q)`
  - Tauchpumpen-Leistung: `P = ρ·g·Q·H/η`
  - Optimaler Bohrloch-Abstand für ≥ 25 Jahre Lebensdauer
  - Spezifische Wärmeleistung [W/m Bohrung]
- **Sekundärkreis-Empfehlungen**
  - COP nach **Carnot × 50 %** (IEA Heat Pump Annex 35)
  - Elektrische WP-Leistung: `W_el = Q_geo / (COP − 1)` *(thermodynamisch korrekt)*
  - WP-Typ-Empfehlung basierend auf Temperaturhub (Direktnutzung / Standard / HT-WP / ORC)
  - LMTD-Gegenstrom-Wärmetauscher-Dimensionierung: `A = Q_th / (U × LMTD)`
- **Materialklassifizierung**
  - TDS-basiert: 1.4571 (Edelstahl) · 1.4462 (Duplex) · Titan/Hastelloy
  - Scaling-Risiko-Bewertung (nach DIN 4030)
- **Transmissivität**: `T = k_f × Mächtigkeit`

### UX
- **Echtzeit-Update**: Alle Berechnungen live, ohne Submit-Button
- **Kompakter Primärkreis** mit 3×3 KPI-Chips und ausklappbarer Detailtabelle
- **Empfehlungs-Bar** – priorisierte Handlungsempfehlungen mit 1-Klick-Schnellkorrektur
- **Ampel-Statusleiste**: 5 Indikatoren (Hydraulik · Thermik · Durchbruch · COP · Material)
- **Info-Popups**: Für jeden Parameter erklärt, was er bedeutet und wie er die anderen Werte beeinflusst
- **Freie Texteingabe** für T_VL und T_RL (Werte außerhalb des Slider-Bereichs möglich)
- **Mobile-optimiert**: Responsives Layout mit gestapelten Spalten auf schmalen Geräten
- **localStorage-Persistenz**: Eingabewerte bleiben zwischen Besuchen erhalten (opt-in via Cookie-Banner)
- **Drucken / PDF-Export**: Browser-Druckdialog mit Print-optimiertem CSS
- **Feedback-System**: Bug-Reports, Ideen & Wünsche landen strukturiert in Supabase
- **Quellen-Modal**: Alle verwendeten Normen und Literaturstellen sichtbar (VDI 4640, EN 14511, Drost 1978, Arpagaus et al. 2018 …)
- **DSGVO-konform**: Cookie-Consent, vollständiges Impressum

---

## 🚀 Schnellstart

```bash
# 1. Repository klonen
git clone https://github.com/cryptoclemens/vencly-delta-t.git
cd vencly-delta-t

# 2. Supabase-Credentials einrichten (optional, für Feedback-Feature)
cp config.example.js config.js
# config.js öffnen und mit deinen Supabase-Keys befüllen

# 3. Starten — delta-t.html im Browser öffnen
open delta-t.html        # macOS
xdg-open delta-t.html    # Linux
start delta-t.html       # Windows
```

Kein `npm install`, keine Build-Pipeline. Die App läuft direkt aus dem `file://`-Protokoll.

---

## 🔧 Konfiguration

Supabase-Credentials kommen aus `config.js` (gitignored, **nie committen**):

```js
window.VENCLY_CONFIG = {
  supabaseUrl:     'https://DEIN-PROJEKT.supabase.co',
  supabaseAnonKey: 'eyJ...'   // Legacy JWT anon key (unter "Legacy API Keys" im Dashboard)
};
```

> ⚠️ Den neuen `sb_publishable_...`-Key **nicht** verwenden – er funktioniert nur mit dem Supabase SDK, nicht mit direkten REST-Aufrufen.

Ohne `config.js` läuft die App im **Dev-Modus** – das Feedback-Formular ist sichtbar, speichert aber nichts.

Vollständige Anleitung: → **[SUPABASE_SETUP.md](SUPABASE_SETUP.md)**

---

## 📂 Projektstruktur

```
vencly-delta-t/
├── delta-t.html              # Haupt-App (React + Babel, single-file ~1800 Zeilen)
├── vencly-project-template.html  # Wiederverwendbares Vencly-Projekt-Template
├── config.js                 # Supabase-Credentials (gitignored)
├── config.example.js         # Vorlage für config.js
├── favicon.png / favicon.ico # Branding
├── DeltaT_Formelprüfung.xlsx # Alle 21 Formeln mit Literaturquellen
│
├── README.md                 # Diese Datei
├── BRIEF.md                  # Produktvision, Zielgruppe, Feature-Scope
├── CLAUDE.md                 # Architektur & Konventionen für Claude Code
├── PLAUSI_CHECK.md           # Wissenschaftlicher Plausi-Check (alle Formeln & Bugs)
├── SUPABASE_SETUP.md         # Supabase-Einrichtung + SQL-Schema
├── Tasks.md                  # Feature-Tracker & Backlog
│
├── .github/workflows/
│   └── deploy.yml            # GitHub Pages Deployment
│
└── .gitignore                # Schließt config.js, .claude/, IDE-Files aus
```

---

## 🏗 Technisches

- **Frontend**: React 18 (UMD via CDN) + Babel Standalone für JSX-Transpilierung im Browser
- **Backend**: Supabase (Postgres + RLS) für Feedback-Persistenz
- **Deployment**: GitHub Pages via GitHub Actions
- **Keine Build-Tools**: Kein Webpack, kein Vite, kein npm
- **Versionierung**: Dynamisch generiert aus aktuellem Datum — Format `v{Jahr}.W{KW}.{HHMM}`

### Berechnungsparameter (12 Eingabewerte)

| Kategorie | Parameter | Einheit | Default | Bereich |
|---|---|---|---|---|
| Geologie | Reservoirtiefe | m | 500 | 100–2000 |
| Geologie | Aquifer-Mächtigkeit | m | 40 | 5–200 |
| Geologie | Hydraul. Leitfähigkeit k_f | m/s | 1e-4 | 1e-6 bis 1e-2 (log) |
| Geologie | Grundwassertemp. T_GW | °C | 25 | 8–80 |
| Geologie | Mineralgehalt TDS | mg/l | 500 | 0–50.000 |
| Betrieb | Förderrate Q | l/s | 15 | 1–100 |
| Betrieb | Reinjektionstemp. T_R | °C | 12 | 5–40 |
| Betrieb | Bohrloch-Abstand | m | 500 | 20–2000 |
| Betrieb | Laufstunden | h/a | 2.000 | 500–8.760 |
| Ziel | Wärmeleistung | kW | 5.000 | 50–10.000 |
| Ziel | Vorlauf T_VL | °C | 90 | **frei eingebbar** (Slider: 30–150) |
| Ziel | Rücklauf T_RL | °C | 55 | **frei eingebbar** (Slider: 10–90) |

---

## 🌐 Deployment (GitHub Pages)

Das Repo ist auf automatisches Deployment via GitHub Actions vorbereitet.

**Einmaliges Setup:**

1. **GitHub Secrets** setzen unter **Settings → Secrets and variables → Actions**:
   ```
   SUPABASE_URL      = https://DEIN-PROJEKT.supabase.co
   SUPABASE_ANON_KEY = eyJ...   (Legacy JWT anon key)
   ```

2. **GitHub Pages** aktivieren unter **Settings → Pages**:
   - Source: `GitHub Actions`

3. **Deploy auslösen**: Jeder Push auf `main` triggert automatisch das Deployment.
   ```bash
   git push origin main
   ```

Die Workflow-Datei `.github/workflows/deploy.yml` erstellt `config.js` aus den Secrets zur Build-Zeit und pusht den kompletten Ordner zu GitHub Pages.

---

## 🔬 Wissenschaftliche Basis

Alle Berechnungen basieren auf etablierten Normen und peer-reviewed Fachliteratur. Die wichtigsten Quellen:

| Referenz | Anwendung |
|---|---|
| **VDI 4640** Blatt 1–4 | Thermische Nutzung des Untergrundes |
| **EN 14511** | Leistungsdefinition Wärmepumpen |
| **Drost (1978)** | Durchbruchszeit-Formel für Dubletten |
| **Arpagaus et al. (2018)** | Hochtemperatur-WP Klassifikation |
| **Zühlsdorf et al. (2019)** | Ultra-HT-WP / ORC Bereich |
| **DVGW W 115** | Werkstoffauswahl Thermalwasser |
| **Kühn (2012)** | Transmissivitäts-Richtwerte |
| **IEA HPP Annex 35** | Reale COP vs. Carnot-COP |
| **VDI Wärmeatlas** (2019) | LMTD & U-Wert Plattenwärmetauscher |
| **DIN 4030** | Scaling-Bewertung |

Vollständiger Review mit allen Formeln, gefundenen Bugs und deren Behebung:
→ **[PLAUSI_CHECK.md](PLAUSI_CHECK.md)**

Komplette Formelprüfung mit Literaturquellen (21 Formeln):
→ **[DeltaT_Formelprüfung.xlsx](DeltaT_Formelprüfung.xlsx)**

---

## ⚠ Disclaimer

Dieser Rechner liefert eine **Vorauslegung auf Machbarkeitsebene**. Für konkrete Investitionsentscheidungen oder Genehmigungsverfahren sind erforderlich:

- Hydrogeologisches Gutachten mit Wasseranalyse (Cl⁻, H₂S, pH, T)
- 3D-Simulation der Wärmeausbreitung (FEFLOW, TOUGH2 oder COMSOL)
- Standortspezifische Untersuchungen zu Aquifer-Heterogenität
- Wirtschaftlichkeitsberechnung unter Berücksichtigung lokaler Strompreise & Förderung

---

## 🛣 Roadmap

Siehe **[Tasks.md](Tasks.md)** für den aktuellen Feature-Stand.

**Nächste geplante Features:**
- Karten-Visualisierung mit Geo-Daten (Leaflet.js + GeoTIFF)
- Mehrstandort-Vergleich
- Einbindung geologischer Karten
- Saisonale Jahresganglinien
- Supabase Auth (Login + gespeicherte Konfigurationen pro Nutzer)

---

## 📝 Mitwirken

Feedback, Bug-Reports und Feature-Wünsche gerne über das **Feedback-Panel** direkt in der App (wird in Supabase gespeichert) oder als GitHub Issue.

---

## 📄 Lizenz & Impressum

**vencly GmbH**
Leopoldstraße 31, 80802 München
HRB 290524 (AG München) · USt-ID: DE367131457
📧 [hello@vencly.com](mailto:hello@vencly.com) · 🌐 [www.vencly.com](https://www.vencly.com)

Vertreten durch Clemens Eugen Theodor Pompeÿ.

---

Built with ♥ using [Claude Code](https://claude.com/claude-code) · [Vencly](https://www.vencly.com)
