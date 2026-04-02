# DeltaT – Geothermischer Dubletten-Auslegungsrechner

Interaktiver Echtzeit-Rechner für geothermische Dubletten-Systeme im Lockergestein.
Entwickelt von [vencly GmbH](https://vencly.com), München.

**Live:** [cryptoclemens.github.io/vencly-delta-t/delta-t.html](https://cryptoclemens.github.io/vencly-delta-t/delta-t.html)

---

## Features

- **Primärkreis** – ΔT, thermische Leistung, Doubletten-Anzahl, Jahreswärmemenge, Durchbruchszeit
- **Sekundärkreis** – WP-Typ-Empfehlung (nach ΔT_hub), COP, el. WP-Leistung, Wärmetauscher-Dimensionierung
- **Material & Scaling** – TDS-basierte Werkstoffklasse und Scaling-Risikobewertung
- **Empfehlungs-Bar** – Priorisierte Handlungsempfehlungen mit 1-Klick-Schnellkorrektur
- **Ampel-Statusleiste** – 5 Indikatoren (Hydraulik, Thermik, Durchbruch, COP, Material)
- **Echtzeit** – alle Parameter live ohne Submit-Button
- **Feedback-System** – strukturiert in Supabase gespeichert
- **DSGVO-konform** – Cookie-Consent, localStorage-Persistenz, vollständiges Impressum

## Schnellstart

```bash
# 1. Repository klonen
git clone https://github.com/cryptoclemens/vencly-delta-t.git
cd vencly-delta-t

# 2. Konfiguration einrichten
cp config.example.js config.js
# config.js öffnen und Supabase-Credentials eintragen (JWT anon key, nicht sb_publishable_!)

# 3. Starten
open delta-t.html   # kein Build-System nötig
```

## Konfiguration

Credentials kommen aus `config.js` (gitignored, nie committen):

```js
window.VENCLY_CONFIG = {
  supabaseUrl:     'https://xxxx.supabase.co',
  supabaseAnonKey: 'eyJ...',   // Legacy JWT anon key (unter "Legacy API Keys" im Dashboard)
};
```

> ⚠️ Den neuen `sb_publishable_...`-Key **nicht** verwenden – er funktioniert nur mit dem Supabase SDK, nicht mit direkten REST-Aufrufen.

Vollständige Anleitung: → [SUPABASE_SETUP.md](SUPABASE_SETUP.md)

## Deployment (GitHub Pages)

```bash
# GitHub Secrets setzen:
# Settings → Secrets and variables → Actions
#   SUPABASE_URL      = https://xxxx.supabase.co
#   SUPABASE_ANON_KEY = eyJ...   (Legacy JWT anon key)

git push origin main   # GitHub Actions übernimmt den Rest
```

## Dateien

| Datei | Beschreibung |
|---|---|
| `delta-t.html` | Gesamte App (React 18, Single-File, kein Build-System) |
| `config.js` | Supabase-Credentials (**gitignored** – nie committen) |
| `config.example.js` | Vorlage für config.js |
| `favicon.png / .ico` | Vencly-Logo |
| `DeltaT_Formelprüfung.xlsx` | Alle 21 Formeln mit Literaturquellen (für externen Review) |
| `CLAUDE.md` | Architektur & Konventionen für Claude Code |
| `BRIEF.md` | Produktvision, Zielgruppe, Feature-Scope |
| `TASKS.md` | Backlog & technische Schulden |
| `PLAUSI_CHECK.md` | Wissenschaftlicher Formeln-Review |
| `SUPABASE_SETUP.md` | Supabase-Einrichtung + SQL-Schema |
| `.github/workflows/deploy.yml` | GitHub Pages Deployment |

## Wissenschaftliche Grundlage

Alle Berechnungen basieren auf Normen und peer-reviewed Literatur:
VDI 4640 · EN 14511 · Arpagaus et al. 2018 · Zühlsdorf et al. 2019 · Drost 1978 · VDI Wärmeatlas 2019

Vollständige Quellenliste: `DeltaT_Formelprüfung.xlsx` (Sheet 4)

## Roadmap

Siehe [TASKS.md](TASKS.md) für den vollständigen Backlog.

---

Built with ♥ and [Claude](https://claude.ai) · [vencly.com](https://vencly.com)
