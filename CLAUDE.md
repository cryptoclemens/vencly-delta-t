# CLAUDE.md – Arbeitsanleitung für Claude Code

Dieses Dokument beschreibt die Architektur, Konventionen und kritischen Details des DeltaT-Projekts für eine reibungslose Weiterarbeit mit Claude Code.

---

## Projektübersicht

**DeltaT** ist ein geothermischer Dubletten-Auslegungsrechner als Single-File-React-App. Die gesamte Anwendung lebt in `delta-t.html` – kein Build-System, kein npm, kein Bundler. Die Datei wird direkt im Browser geöffnet oder via GitHub Pages ausgeliefert.

**Live-URL:** `https://cryptoclemens.github.io/vencly-delta-t/delta-t.html`

---

## Architektur

```
delta-t.html          ← Gesamte App (React via CDN, Babel-Transpilation im Browser)
config.js             ← Supabase-Credentials (gitignored, nie committen!)
config.example.js     ← Vorlage für config.js
favicon.png / .ico    ← Vencly-Logo (programmatisch generiert)
.github/workflows/    ← GitHub Actions: deploy.yml injiziert SUPABASE_ANON_KEY in config.js
```

### Stack
- **React 18** (CDN, UMD-Build) + **Babel Standalone** (JSX-Transpilation im Browser)
- **Supabase JS SDK v2** (CDN) für Feedback-Speicherung
- **Kein Build-Schritt** – alles inline in einer HTML-Datei

### Komponenten-Struktur (in delta-t.html)
```
App
├── Topbar
├── InputColumn          ← 12 ParamSlider-Komponenten mit InfoPopup (i-Button)
├── PrimaryColumn        ← KPI-Tiles (3×3), ausklappbare Detailtabelle
├── SecondaryColumn      ← WP-Empfehlung, COP, Wärmetauscher, Material, Scaling
├── RecommendationBar    ← Statisch über StatusBar, priorisierte Empfehlungen
├── StatusBar            ← 5 Ampeln + Quellen/Impressum-Buttons
├── CookieBanner         ← Erscheint bei erstem Besuch
├── QuellenModal         ← Literaturquellen-Popup
└── ImpressumModal       ← Vollständiges Impressum
```

---

## Berechnungsengine (`calculateSystem`)

Alle Berechnungen laufen in der reinen Funktion `calculateSystem(inp)`. Sie hat **keinen Side-Effect** und wird via `useMemo` bei jeder Input-Änderung neu ausgeführt.

### Eingaben (12 Parameter)
| Variable | Bedeutung | Einheit |
|---|---|---|
| `tiefe` | Reservoirtiefe | m |
| `maechtig` | Aquifer-Mächtigkeit | m |
| `kf` | Hydraulische Leitfähigkeit | m/s (log-Skala) |
| `tGW` | Grundwassertemperatur | °C |
| `tds` | TDS / Mineralisation | mg/l |
| `Q` | Förderrate | l/s |
| `tR` | Reinjektionstemperatur | °C |
| `abstand` | Bohrloch-Abstand | m |
| `zielLeistung` | Ziel-Wärmeleistung | kW |
| `tVL` | Vorlauftemperatur | °C |
| `tRL` | Rücklauftemperatur | °C |
| `laufstunden` | Betriebsstunden/Jahr | h/a |

### Schlüsselformeln (mit Quellen)
```
T            = kf × b                                      [m²/s]   VDI 4640 Bl. 1
ΔT           = T_GW − T_R                                  [K]
Q_th         = Q × 4.18 × ΔT                               [kW]     Sanner et al. 2003
n_D          = ⌈Q_Ziel / Q_th⌉                             [–]
Q_ges        = n_D × Q_th                                  [kW]
P_pump       = (Q/1000) × 1000 × 9.81 × h / (0.6 × 1000)  [kW]     VDI 3803 (η=0.6)
t_break      = π×n×b×d² / (4×Q_m) / (365×86400)           [Jahre]  Drost 1978
d_opt        = √(4×Q_m×25×365×86400 / (π×0.25×b))         [m]      Drost 1978
COP          = (T_VL_K / (T_VL_K − T_GW_K)) × 0.50        [–]      Arpagaus 2018
P_el         = Q_ges / (COP − 1)                           [kW]     EN 14511
LMTD         = (ΔT₁ − ΔT₂) / ln(ΔT₁/ΔT₂)                 [K]      VDI Wärmeatlas
A_WT         = Q_ges × 1000 / (4000 × LMTD)               [m²]     U=4000 W/(m²K)
ΔT_hub       = T_VL − T_GW                                 [K]      WP-Klassifikation
E_a          = Q_ges × laufstunden / 1000                  [MWh/a]
```

**Vollständige Formeldokumentation:** `DeltaT_Formelprüfung.xlsx` (Sheet 2 + Sheet 3)

---

## Supabase / Feedback

Feedback wird in der Supabase-Tabelle `feedback` gespeichert.

```js
// Credentials kommen aus config.js (lokal) bzw. GitHub Secret (CI/CD)
window.VENCLY_CONFIG = {
  supabaseUrl:     'https://xwviiooyqxduokriiuzp.supabase.co',
  supabaseAnonKey: 'eyJ...',   // JWT anon key (NICHT sb_publishable_... verwenden!)
};
```

**Wichtig:** Der neue Supabase-Schlüsselformat `sb_publishable_...` funktioniert **nicht** mit direkten REST-Fetches. Immer den Legacy-JWT-Key (`eyJ...`) verwenden, der unter „Legacy anon/service_role keys" im Dashboard zu finden ist.

### GitHub Actions
`.github/workflows/deploy.yml` baut `config.js` aus den Secrets `SUPABASE_URL` und `SUPABASE_ANON_KEY` und deployt auf GitHub Pages.

---

## localStorage / Cookie-Consent

```
deltat_cookie_consent   ← 'accepted' | 'declined'
deltat_inputs           ← JSON der 12 Eingabeparameter (nur bei 'accepted')
```

Inputs werden bei jeder Änderung in `setInput()` gespeichert, wenn Consent erteilt.

---

## Konventionen

- **Alle Änderungen nur in `delta-t.html`** – keine separaten JS/CSS-Dateien
- **Keine Build-Tools** – kein npm install, kein webpack
- **Commit-Sprache:** Deutsch (Commit-Messages auf Deutsch, Code-Kommentare auf Deutsch/Englisch gemischt)
- **Versionsnummer** im Topbar: `v2026.W14.2340` (Format: Jahr.KW.HHMM) – manuell pflegen
- **config.js ist gitignored** – niemals committen, Vorlage in `config.example.js`

---

## Bekannte Gotchas

1. **Supabase Key-Format:** `sb_publishable_...` ≠ JWT `eyJ...` – nur JWT für direkte REST-Calls
2. **ReactDOM.createPortal** für alle Popups nötig – sonst werden sie von `.calc-col { overflow-y: auto }` abgeschnitten
3. **Single-File-Einschränkung:** Alle Stile, Komponenten und Logik in einer Datei – bei weiterem Wachstum sollte ein Build-System (Vite) evaluiert werden
4. **GitHub Actions Merge-Konflikt:** Bei Parallel-Pushes kann `git pull --rebase` nötig sein, bevor `git push` funktioniert

---

## Nächste sinnvolle Entwicklungsschritte

Siehe `TASKS.md` für den vollständigen Rückstand. Technische Prioritäten:

1. **Migration zu Vite + React (Multi-File)** – wenn die Datei > 2000 Zeilen überschreitet
2. **Geologische Kartenlayer** (z.B. Leaflet + GeoTIFF) für standortbasierte Vorbelegung der Parameter
3. **Mehrstandort-Vergleich** – State-Management für mehrere Parametersätze
4. **Supabase Auth** – gespeicherte Konfigurationen pro Nutzer
