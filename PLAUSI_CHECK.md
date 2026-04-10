# Wissenschaftlicher Plausi-Check – DeltaT Rechner

*Stand: März 2026 | Autor: Claude (Anthropic) | Quellen: peer-reviewed Fachliteratur*

---

## Zusammenfassung

Der Rechner ist konzeptionell korrekt aufgebaut. Drei kritische Rechenfehler wurden in früheren Reviews identifiziert und behoben (Q_th ×1000, Tauchpumpe ×82, WP-Elektrik COP/(COP−1)). Im zweiten Review (März 2026) wurde ein vierter konzeptioneller Fehler behoben: Die Dubletten-Anzahl ignorierte den WP-Beitrag und überdimensionierte das System. Mehrere Vereinfachungen sind für eine Vorauslegung akzeptabel, sollten aber bei der Interpretation der Ergebnisse bekannt sein.

---

## 1. Thermische Leistungsformel ✅ (Bug behoben)

**Formel im Code (alt):** `Q_th = Q [l/s] × ΔT × 4,18 / 1000`

**Problem:** Die Division durch 1000 war falsch. Sie ergab Ergebnisse um den Faktor 1000 zu klein (statt 250 kW → 0,25 kW pro Dublette).

**Korrigierte Formel:** `Q_th [kW] = Q [l/s] × c_p [kJ/(kg·K)] × ΔT [K]`

Da Wasser eine Dichte von ~1 kg/l hat, gilt Q [l/s] ≈ ṁ [kg/s]. Mit c_p = 4,18 kJ/(kg·K) (Wasser bei 15–20 °C) ergibt sich die korrekte Leistung in kW ohne weitere Umrechnungsfaktoren.

**Literatur:** VDI 4640 Blatt 1 (Thermische Nutzung des Untergrundes), DVGW W 100.

---

## 2. Tauchpumpenleistung ✅ (Bug behoben)

**Formel im Code (alt):** `P = Q × 0,02 × Tiefe / 100` → ~1,5 kW (bei Q=15 l/s, 500 m)

**Problem:** Ergebnis war ca. 80× zu niedrig. Korrekte hydraulische Leistungsformel:

**Korrigierte Formel:** `P_el [kW] = Q [m³/s] × ρ × g × H / η`
= `Q [l/s] × 9,81 × H [m] / (η × 1000)`

Mit η = 0,6 (Tauchpumpe + Motor): Bei Q=15 l/s und H=500 m → **P_el ≈ 122 kW**

**Literatur:** DIN EN ISO 9906, Sulzer Pumpen-Technik Handbuch.

---

## 3. Durchbruchszeit ⚠️ (vereinfacht, konservativ optimistisch)

**Formel:** `t_break = (π × n × b × d²) / (4 × Q)`

Dies ist die klassische analytische Lösung nach Drost (1978) für einen homogenen, isotropen Aquifer mit idealem Radialfluss.

**Was stimmt:** Die Formel ist physikalisch korrekt und wird international für Vorabschätzungen verwendet.

**Was fehlt / wo ist die Formel zu optimistisch:**
- **Heterogenität:** Reale Aquifere haben bevorzugte Fließwege (Klüfte, Schichtung). In heterogenen Systemen kann der Durchbruch 30–50 % früher eintreten als berechnet.
- **Thermische Dispersion:** Wärmeausbreitung im Aquifer erfolgt auch durch Diffusion und Dispersion – im Modell nicht berücksichtigt.
- **Wärmeaustausch mit Deckschichten:** Die thermischen Eigenschaften der überlagernden Schichten verlängern die Lebensdauer signifikant (können Dubletten-Lebensdauer verdoppeln).
- **Regel aus der Paris-Becken-Studie:** Doublet-Lebensdauer ≈ 2 × Durchbruchszeit (Maget et al., 2023).

**Empfehlung für die Praxis:** Sicherheitsfaktor von 1,5–2,0 auf die berechnete Durchbruchszeit anwenden. Für Projekte >1 MW: numerische 3D-Simulation (FEFLOW, TOUGH2, COMSOL) unerlässlich.

**Literatur:**
- Maget et al. (2023): *Doublet lifetime and thermal breakthrough at the Dogger reservoir*. Geothermics.
- Willems et al. (2017): *Interference in heat production from geothermal doublets*. Energy.
- TU Delft Repository: *Breakthrough time of a geothermal reservoir* (COMSOL 3D-Sensitivity-Studie).

---

## 4. Elektrische WP-Leistung ✅ (Bug behoben)

**Formel im Code (alt):** `P_el = Q_th_gesamt / COP`

**Problem:** COP ist definiert als Q_geliefert / W_el, wobei Q_geliefert = Q_geo + W_el (Wärmepumpe addiert elektrische Energie zur Quellwärme). Daraus folgt:

**Korrigierte Formel:** `P_el [kW] = Q_geo / (COP − 1)`

**Numerisches Beispiel** (T_VL = 90 °C, T_GW = 16 °C → COP = 2,45):
- Alter Code: P_el = 5000/2,45 = 2.041 kW → COP-Check: (5000+2041)/2041 = **3,45 ≠ 2,45** ❌
- Korrigiert: P_el = 5000/(2,45−1) = 3.448 kW → COP-Check: (5000+3448)/3448 = **2,45** ✅

Der Fehler unterschätzte die elektrische Leistung um den Faktor COP/(COP−1), bei COP 2,45 also um **~69 %**.

**Literatur:** IEA Heat Pump Programme Annex 47, EN 14511 (COP-Definition für WP).

---

## 5. COP-Formel ✅ (wissenschaftlich solide)

**Formel:** `COP = (T_VL [K] / (T_VL [K] – T_GW [K])) × 0,5`

Dies ist der **Carnot-COP × 50 %** – eine in der Praxis gut etablierte Näherung.

**Was die Literatur sagt:**
- Reale Wärmepumpen erreichen typischerweise 45–55 % des Carnot-Idealwerts (OpenEnergyMonitor, 2024).
- In der größten Feldstudie mit 1023 Wärmepumpen (Nature Communications, 2025) lag der Mittelwert erdgekoppelter Systeme bei ~3,5–4,5 COP bei 35–55 °C Vorlauftemperatur.
- Bei T_VL = 90 °C (Fernwärme) sind COP-Werte von 2,5–3,5 realistisch → **der Rechner liegt im richtigen Bereich**.

**Einschränkung:** Die Formel verwendet T_GW als Quellentemperatur der WP. Realistischer wäre T_GW minus ~2–5 K Wärmetauscher-Pinch.

---

## 6. LMTD & Wärmetauscherfläche ✅ (korrekt)

**Formel:** LMTD-Gegenstrom, A = Q_th / (U × LMTD) mit U = 4.000 W/(m²·K)

**Bewertung:** Standardformel für Plattenwärmetauscher. Der angesetzte U-Wert von 4.000 W/(m²·K) liegt im literaturkonformen Bereich für Wasser-Wasser-PHEs (2.000–6.000 W/(m²·K) je nach Verschmutzungsgrad, VDI Wärmeatlas).

---

## 7. Transmissivität ✅ (korrekt)

**Formel:** `T = k_f × b`

Standarddefinition der hydraulischen Transmissivität für gespannte Aquifere. Keine Beanstandungen.

---

## 8. Materialklassen (TDS-Schwellwerte) ⚠️ (vereinfacht)

**Was stimmt:** Die Grundtendenz ist richtig – mehr gelöste Stoffe → korrosiveres Wasser → hochwertigere Werkstoffe.

**Was fehlt:** Korrosion wird primär durch **Chloridgehalt**, pH-Wert und Temperatur gesteuert, nicht allein durch TDS. Belastbarere Einstufung via **PREN-Index** (Pitting Resistance Equivalent Number):
- 1.4571 (316Ti, PREN ≈ 24): bis ~200 mg/l Cl⁻
- 1.4462 (Duplex, PREN ≈ 33): bis ~1.000 mg/l Cl⁻
- Titan Grade 2: praktisch resistent

**Empfehlung:** Bei konkreter Planung: Wasseranalyse mit Chlorid, H₂S, pH, Temperatur.

**Literatur:** DVGW W 115, VDI 4640 Blatt 3.

---

## 9. Hydraulik-Ampel ✅ (korrigiert)

**Code (aktuell):** `Transmissivität T = k_f × Mächtigkeit > 1×10⁻³ m²/s → grün; > 1×10⁻⁴ → gelb; sonst rot`

**Bewertung:** Die Hydraulik-Ampel nutzt jetzt korrekt die Transmissivität T statt nur k_f. Die Schwellwerte sind konform mit VDI 4640 (T > 10⁻³ m²/s für wirtschaftliche Dubletten).

**Literatur:** VDI 4640 Blatt 1, Kühn (2012).

---

## 10. Dubletten-Dimensionierung mit WP-Beitrag ✅ (Bug behoben)

**Formel im Code (alt):** `n = ceil(Q_Ziel / Q_th_Doublette)`

**Problem:** Die Zielleistung wurde als reines Geothermie-Extraktionsziel behandelt. Bei aktiver Wärmepumpe addiert diese jedoch W_el zur Quellwärme: Q_geliefert = Q_geo + W_el = Q_geo × COP/(COP−1). Das System wurde systematisch überdimensioniert.

**Numerisches Beispiel** (T_GW = 25 °C, T_VL = 90 °C, COP = 2,79):
- Alter Code: Q_geo_benötigt = 5.000 kW → viele Doubletten für volle Zielleistung
- Korrigiert: Q_geo_benötigt = 5.000 × (2,79−1)/2,79 = **3.208 kW** → weniger Doubletten nötig
- Q_geliefert = Q_geo × COP/(COP−1) = Q_geo + W_el ≥ 5.000 kW ✅

**Korrigierte Formel:** `Q_geo_benötigt = Q_Ziel × (COP−1)/COP` (wenn WP aktiv, d.h. T_VL > T_GW)

Bei Direktnutzung (T_GW ≥ T_VL) gilt weiterhin: `Q_geo_benötigt = Q_Ziel`.

---

## 11. Default-Werte ✅ (korrigiert)

**Problem:** T_GW = 16 °C bei Tiefe = 500 m war inkonsistent mit dem geothermischen Gradienten (~3 °C/100 m). Bei 500 m Tiefe und einer Oberflächentemperatur von ~10 °C ergibt sich T_GW ≈ 25 °C.

**Korrektur:** T_GW-Default auf 25 °C, Bohrloch-Abstand auf 500 m angehoben (war 100 m → Durchbruchszeit nur 2 Monate).

---

## 12. Thermik-Ampel ✅ (korrigiert)

**Problem:** Die alte Thermik-Ampel verglich `Q_th_Doublette ≥ Q_Ziel/n × 0,95`. Da `n = ceil(Q_Ziel/Q_th)`, war diese Bedingung mathematisch immer erfüllt → Ampel war stets grün.

**Korrektur:** Die Ampel vergleicht jetzt die tatsächlich gelieferte Leistung (Q_geo + W_el) mit der Zielleistung: grün ≥ 99 %, gelb ≥ 80 %, rot < 80 %.

---

## 13. Forschungsstand: Optimales ΔT

Es gibt **keine universelle** wissenschaftliche Antwort auf „das ideale ΔT". Die optimale Reinjektionstemperatur ist ein Abwägungsproblem:

| Faktor | Niedrige T_R (großes ΔT) | Hohe T_R (kleines ΔT) |
|---|---|---|
| Thermische Leistung | hoch | niedrig |
| Durchbruchszeit | kürzer | länger |
| COP der WP | schlechter | besser |
| Anzahl Bohrungen | weniger | mehr |
| Lebensdauer der Anlage | kürzer | länger |

**Schlussfolgerung aus der Literatur** (Willems et al. 2017, Hannover-Studie 2024): Für eine Laufzeit >30 Jahre sollte das System mit der Randbedingung **t_break > 25–30 Jahre** ausgelegt werden – nicht nach maximaler ΔT-Ausbeute. Der Rechner implementiert genau dieses Optimierungsprinzip (Ampel „Durchbruchszeit").

---

## Fazit

| Formel | Bewertung |
|---|---|
| Thermische Leistung | ✅ korrigiert (Review 1: war ×1000 falsch) |
| Tauchpumpenleistung | ✅ korrigiert (Review 1: war ×82 zu niedrig) |
| Elektr. WP-Leistung | ✅ korrigiert (Review 1: Q/COP → Q/(COP−1)) |
| Dubletten-Dimensionierung | ✅ korrigiert (Review 2: WP-Beitrag berücksichtigt) |
| Durchbruchszeit (Drost) | ✅ korrekt, aber vereinfacht |
| COP (Carnot×50%) | ✅ wissenschaftlich valide |
| LMTD & WT-Fläche | ✅ korrekt |
| Transmissivität | ✅ korrekt |
| Hydraulik-Ampel | ✅ korrigiert (nutzt jetzt T statt nur k_f) |
| Thermik-Ampel | ✅ korrigiert (Review 2: war immer grün) |
| Default-Werte | ✅ korrigiert (Review 2: T_GW & Abstand plausibel) |
| Materialklassen | ⚠️ TDS zu simpel, Cl⁻ fehlt |

Der Rechner ist **für eine erste Vorauslegung (Machbarkeitsebene) geeignet**. Für Investitionsentscheidungen und Genehmigungsverfahren sind numerische 3D-Simulationen und hydrogeologische Gutachten erforderlich.
