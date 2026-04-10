# Tasks – Vencly DeltaT (Geothermie Auslegungsrechner)

**Fortschritt: 80 %** (8 von 10 Kernfeatures erledigt)

---

## ✅ Erledigt

### Berechnungslogik (CALC)
- [x] **Primärkreis-Berechnung** – Thermische Leistung, Anzahl Doubletten, Durchbruchszeit
- [x] **Sekundärkreis-Auslegung** – COP, Wärmetauscher-Dimensionierung (LMTD)
- [x] **WP-Dimensionierung & COP** (Carnot × 50 %)
- [x] **Wärmetauscher-Dimensionierung** – LMTD-Gegenstrom, A = Q/(U·LMTD)
- [x] **Bugfix**: Q_th-Formel ×1000 (Review 1)
- [x] **Bugfix**: Tauchpumpen-Formel ×82 (Review 1)
- [x] **Bugfix**: WP-Elektrik Q/(COP−1) statt Q/COP (Review 1)
- [x] **Bugfix**: Dubletten-Dimensionierung berücksichtigt WP-Beitrag (Review 2)

### Benutzeroberfläche (UI)
- [x] **Geologische Eingabeparameter** (11 Parameter, gruppiert in Geologie/Betrieb/Ziel)
- [x] **Ampel-Statusanzeige** (Hydraulik, Thermik, Durchbruch, COP, Material)
- [x] **Info-Popups** für alle Parameter mit Erklärung und Richtwerten
- [x] **Echtzeit-Update** ohne Submit-Button
- [x] **Drucken / PDF-Export** via Browser-Druckdialog
- [x] **Cookie-Banner + localStorage-Persistenz**
- [x] **Quellen-Modal** mit allen Literatur-Referenzen
- [x] **Impressum-Modal** (DSGVO-konform)
- [x] **Freie Texteingabe** für T_VL und T_RL ohne Slider-Begrenzung
- [x] **Bugfix**: Thermik-Ampel vergleicht jetzt Q_delivered vs. Ziel
- [x] **Bugfix**: Default T_GW und Bohrloch-Abstand plausibel

### Infrastruktur (DATA)
- [x] **Feedback-System** – strukturierte Speicherung in Supabase mit RLS
- [x] **GitHub Pages Deployment** via Actions (inkl. Secret-Injection)

---

## 🔜 Offen

### Geplant
- [ ] **Karten-Visualisierung (Geo)** `DATA` – Leaflet oder MapLibre GL für Standort-Anzeige
- [ ] **Mehrstandort-Vergleich** `DATA` – Mehrere Dubletten-Konfigurationen parallel
- [ ] **Einbindung geologischer Karten mit Standort-Daten** `UI` – BGR / Geoportal.de Overlay

### Ideen (ungeprüft)
- [ ] Supabase Auth (Login) + gespeicherte Konfigurationen pro Nutzer
- [ ] CSV-Export der Eingabeparameter und Ergebnisse
- [ ] Sensitivitätsanalyse (Tornado-Diagramm)
- [ ] Wirtschaftlichkeitsmodul (CAPEX, OPEX, LCOH)
- [ ] Mehrsprachigkeit (EN / FR)

---

## 🐛 Bekannte Einschränkungen

| Thema | Status | Details |
|---|---|---|
| Materialklasse nur TDS-basiert | ⚠ | Chloridgehalt wäre aussagekräftiger (DVGW W 115) |
| COP nutzt T_GW statt T_Verdampfer | ⚠ | Pinch ~3–5 K nicht berücksichtigt (optimistisch) |
| Drost-Formel homogen/isotrop | ⚠ | Reale Aquifere 30–50 % früher Durchbruch möglich |
| Tauchpumpen-Pumphöhe | ⚠ | Aktuell = Reservoirtiefe, ignoriert Drawdown |

Diese Vereinfachungen sind **für Machbarkeitsebene akzeptabel** und sollten bei der Interpretation berücksichtigt werden. Details in **[PLAUSI_CHECK.md](PLAUSI_CHECK.md)**.
