# BRIEF – DeltaT Geothermie-Auslegungsrechner

**Produkt:** DeltaT
**Betreiber:** vencly GmbH, München
**Status:** Alpha (öffentlich zugänglich, aktiv in Entwicklung)
**Live:** [cryptoclemens.github.io/vencly-delta-t/delta-t.html](https://cryptoclemens.github.io/vencly-delta-t/delta-t.html)

---

## Produktvision

DeltaT ermöglicht es Geothermie-Planern, Ingenieuren und Investoren, die hydraulische und thermische Vordimensionierung eines geothermischen Dubletten-Systems im Lockergestein in Sekunden durchzuführen – direkt im Browser, ohne Installation, ohne Login.

Das Tool ersetzt keine ingenieurtechnische Detailplanung, liefert aber sofort verwertbare Größenordnungen für:
- Thermische Leistung und Anzahl benötigter Doubletten
- WP-Typ-Empfehlung basierend auf Temperaturhub
- Durchbruchsrisiko und optimalen Bohrloch-Abstand
- Jahreswärmemenge und Anlagenwirtschaftlichkeit

---

## Zielgruppe

1. **Geothermie-Planer & Gutachter** – Erste Machbarkeitsabschätzung vor aufwändiger Simulation
2. **Projektierer & Investoren** – Schneller Plausibilitätscheck für Standortbewertung
3. **Kommunen & Stadtwerke** – Eingangsgröße für Wärmenetz-Konzeptstudien
4. **Lehrende & Studierende** – Interaktives Lernwerkzeug für geothermische Systemzusammenhänge

---

## Kernfunktionen (Stand Alpha)

| Feature | Status |
|---|---|
| 12 Eingabeparameter mit Info-Popups | ✅ Live |
| Echtzeit-Berechnung (kein Submit) | ✅ Live |
| Primärkreis: ΔT, Leistung, Doubletten, Durchbruchszeit | ✅ Live |
| Sekundärkreis: COP, WP-Typ, Wärmetauscher, Material | ✅ Live |
| Ampel-Statusleiste (5 Indikatoren) | ✅ Live |
| Handlungsempfehlungs-Bar mit Schnellkorrektur | ✅ Live |
| Jahreswärmemenge [MWh/a] | ✅ Live |
| Cookie-Consent + localStorage-Persistenz | ✅ Live |
| Feedback-System (Supabase) | ✅ Live |
| Impressum + Quellennachweis (Popup) | ✅ Live |
| Wissenschaftliche Formelprüfung (Excel) | ✅ Dokument |
| Geologische Karten / Standortdaten | 🔲 Geplant |
| Mehrstandort-Vergleich | 🔲 Geplant |
| Nutzerkonto / gespeicherte Konfigurationen | 🔲 Geplant |

---

## Wissenschaftlicher Rahmen

Alle Berechnungen basieren auf peer-reviewed Literatur und anerkannten Normen:

- **VDI 4640** (Bl. 1–4) – Thermische Nutzung des Untergrundes
- **EN 14511** – Wärmepumpen-COP-Definition
- **Arpagaus et al. 2018** (Energy) – Hochtemperatur-WP-Klassifikation
- **Zühlsdorf et al. 2019** (JRSE) – Ultra-high lift WP / ORC
- **Drost 1978** – Thermische Durchbruchszeit-Formel
- **VDI Wärmeatlas 2019** – LMTD, Wärmetauscher-Auslegung

Vollständige Quellenliste: → `DeltaT_Formelprüfung.xlsx` (Sheet 4)
Wissenschaftlicher Plausi-Check: → `PLAUSI_CHECK.md`

---

## Abgrenzung (Nicht-Ziele)

- **Kein Ersatz für hydrogeologische Gutachten** – Standortparameter müssen durch Bohrung/Pumpversuch ermittelt werden
- **Kein Genehmigungsersatz** – Wasserrechtliche Erlaubnis bleibt erforderlich
- **Nur Lockergestein** – Kluftaquifere / Karstgeologie außerhalb des Modells
- **Stationäre Betrachtung** – keine Jahresganglinien oder saisonale Speichereffekte

---

## Technologie

Single-File-React-App (kein Build-System), GitHub Pages Hosting, Supabase für Feedback.
Details: → `CLAUDE.md`

---

## Kontakt

vencly GmbH · Leopoldstraße 31 · 80802 München
[hello@vencly.com](mailto:hello@vencly.com) · [vencly.com](https://vencly.com)
