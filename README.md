# DeltaT – Geothermischer Dubletten-Auslegungsrechner

Interaktiver Echtzeit-Rechner für geothermische Dubletten-Systeme im Lockergestein.
Entwickelt mit [Vencly](https://www.vencly.com).

## Features

- **Primärkreis-Auslegung** – ΔT, thermische Leistung, Anzahl Doubletten, Durchbruchszeit
- **Sekundärkreis-Empfehlungen** – WP-Typ, COP, Wärmetauscher-Dimensionierung
- **Materialklassifizierung** – TDS-basiert (Edelstahl / Duplex / Titan)
- **Ampel-Statusleiste** – sofortige Bewertung der Konfiguration
- **Echtzeit** – alle Parameter live ohne Submit-Button
- **Feedback-System** – strukturiert in Supabase gespeichert

## Schnellstart

```bash
# 1. Repository klonen
git clone https://github.com/DEIN-USER/delta-t.git
cd delta-t

# 2. Konfiguration einrichten
cp config.example.js config.js
# config.js öffnen und Supabase-Credentials eintragen

# 3. Starten – einfach delta-t.html im Browser öffnen
#    (kein Build-System nötig)
```

## Konfiguration

Credentials kommen aus `config.js` (gitignored, nie committen):

```js
window.VENCLY_CONFIG = {
  supabaseUrl:     'https://xxxx.supabase.co',
  supabaseAnonKey: 'sb_publishable_...',   // Publishable key (früher: anon key)
};
```

Vollständige Anleitung: → [SUPABASE_SETUP.md](SUPABASE_SETUP.md)

## Deployment (GitHub Pages)

```bash
# GitHub Secrets setzen:
# Settings → Secrets and variables → Actions
#   SUPABASE_URL      = https://xxxx.supabase.co
#   SUPABASE_ANON_KEY = sb_publishable_...   (dein Publishable key)

# Dann pushen – GitHub Actions übernimmt den Rest
git push origin main
```

## Dateien

| Datei | Beschreibung |
|---|---|
| `delta-t.html` | Haupt-App (React, single-file) |
| `vencly-project-template.html` | Wiederverwendbares Projekt-Template |
| `config.js` | Credentials (**gitignored**, lokal anlegen) |
| `config.example.js` | Vorlage für config.js |
| `SUPABASE_SETUP.md` | Supabase-Einrichtung + SQL-Schema |
| `.github/workflows/deploy.yml` | GitHub Pages Deployment |

## Roadmap

- [ ] Supabase Auth / Login
- [ ] Gespeicherte Konfigurationen pro Nutzer
- [ ] PDF-Export
- [ ] Karten-Visualisierung (Geodaten)
- [ ] Mehrstandort-Vergleich

---

Built with ♥ and [Claude](https://claude.ai) · [Vencly](https://www.vencly.com)
