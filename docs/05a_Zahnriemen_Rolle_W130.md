# Zahnriementrieb Rolle → Schenck W130 (Auslegung und Komponenten)

Stand 29.09.2026 – Vorauslegung. Endgültig mit dem Auslegungsprogramm des Riemenherstellers
(z.B. Gates Design Flex Pro) und den Daten der W130 (Wellen-Ø, zulässige Querkraft, Kennlinie) prüfen.

## Randbedingungen

- Rolle: Umfang 1,57 m (Ø 500 mm) → 318 1/min bei 30 km/h, 1699 1/min bei 160 km/h
- Bremse: Schenck W130, max. 10 000 1/min; Wirbelstrombremse → braucht Drehzahl für Moment
- Auslegungsfall: **25 kW Dauer bei 50 km/h** (heutiges Fahrzeug 32 PS), Betriebsfaktor 1,4
  (Einzylinder-Drehmomentstöße)

## Wichtigste Erkenntnisse

1. **Riemengeschwindigkeit hängt nur an der großen Scheibe auf der Rolle**, nicht an der Übersetzung:
   Z112 (Ø 285 mm) → 25 m/s bei 160 km/h, **33 m/s bei 208 km/h**.
   Graugussscheiben sind bis 33 m/s zugelassen (Gates: 6500 ft/min), **ab 30 m/s auswuchten**.
   → Höchstgeschwindigkeit am Prüfstand in der Firmware auf ~200 km/h begrenzen.
2. **3,5:1 (Z32/Z112) ist besser als 4:1 (Z28/Z112):** größere kleine Scheibe → mehr übertragbare
   Leistung, weniger Umfangskraft, Bremse dreht langsamer (einfacher für Lager und Kupplung).
3. **Riemenbreite 85 mm** nötig (50 mm reicht bei 50 km/h nicht).
4. **Keine Riemenscheibe direkt auf die W130-Welle.** Die Riemenvorspannung (einige kN) wäre eine
   Querkraft auf die Bremsenlager, und bei pendelnder Lagerung würde sie die Momentmessung verfälschen.
   → **Vorgelegewelle** mit eigenen Lagern für die kleine Scheibe, dann **drehelastische Kupplung** zur W130.

## Vergleich der Varianten (HTD 8M)

| | A: 4:1 | **B: 3,5:1 (Empfehlung)** |
|---|---|---|
| Scheiben | Z28 (Ø 71,3) / Z112 (Ø 285,2) | **Z32 (Ø 81,5) / Z112 (Ø 285,2)** |
| Bremse bei 30 / 50 / 100 / 160 km/h | 1273 / 2123 / 4246 / 6794 1/min | 1114 / 1858 / 3716 / 5946 1/min |
| Bremse bei 208 km/h (33 m/s Riemen) | 8840 1/min | 7740 1/min |
| Moment Bremse bei 25 kW, 50 km/h | 112 Nm | 128 Nm |
| Umfangskraft bei 25 kW, 50 km/h | 3140 N | 3150 N |
| Tabellenleistung 50 mm bei dieser Drehzahl | ~19 kW | ~23 kW |
| geschätzt 85 mm (×1,7) | ~32 kW | ~39 kW |
| zulässige Fahrzeugleistung bei 50 km/h (÷ 1,4) | ~23 kW | **~28 kW** |
| Zusatzträgheit an der Rolle je 0,1 kgm² Rotor | +1,6 kgm² | +1,2 kgm² |

Tabellenwerte HTD 8M: norelem, Technischer Hinweis Zahnriemen 22062 (übertragbare Leistung an der
kleinen Scheibe, 20/30/50 mm). Zulässige Umfangskraft 8M: 20 mm 1400 N, 30 mm 2100 N, 50 mm 3500 N
(85 mm ≈ 5900 N). Umschlingung der kleinen Scheibe bei 420–500 mm Achsabstand ≈ 150–155° → >6 Zähne im
Eingriff, Faktor c1 = 1,0. 85-mm-Werte sind aus 50 mm hochgerechnet → mit Herstellertabelle prüfen.

Mehr Leistung bei gleicher Breite: **Hochleistungsriemen** (Gates Poly Chain GT Carbon 8MGT,
Optibelt OMEGA HP, ContiTech Synchrochain) – schmalere Scheiben, aber eigenes Profil/eigene Scheiben.

## Komponentenliste Variante B (3,5:1)

| Pos. | Teil | Beispiel / Bezeichnung | Bezug |
|---|---|---|---|
| 1 | Zahnriemenscheibe klein | HTD **32-8M-85**, Taper-Lock | Mädler, riemen-profi.de, HolTech, TYMA |
| 2 | Zahnriemenscheibe groß | HTD **112-8M-85**, Taper-Lock | wie oben |
| 3 | Taper-Spannbuchsen | passend zu den Wellen-Ø (Größe laut Scheibenkatalog) | wie oben |
| 4 | Zahnriemen | HTD **8M-85**, Länge z.B. **1440** (Achsabstand ≈ 420 mm) oder **1600** (≈ 502 mm), Gates PowerGrip HTD / Optibelt OMEGA / ContiTech | Keilriemenexpress, riemen-profi.de, Händler |
| 5 | Vorgelegewelle | Stahlwelle mit Passfedernut, Ø nach Buchse, beidseitig gelagert | Eigenbau / Drehteil |
| 6 | Lager Vorgelegewelle | Rillenkugellager in Stehlagergehäusen (z.B. SKF SNL), **Drehzahlgrenze ≥ 8000 1/min prüfen** – Y-Lager (UCP) oft zu langsam | SKF/FAG-Händler |
| 7 | Kupplung Vorgelege ↔ W130 | drehelastisch, auf 8000 1/min ausgelegt (z.B. KTR ROTEX GS, Lamellen-/Membrankupplung) – oder vorhandene Schenck-Gelenkwelle | KTR, R+W, Mayr |
| 8 | Scheibe/Adapter an der Rolle | Taper-Buchse auf Rollenwelle (Rollenlager auf Riemenzug prüfen) | – |
| 9 | Spannvorrichtung | Vorgelegewelle auf Langlöchern verschiebbar (keine Rückenspannrolle) | Eigenbau |
| 10 | Schutzhaube | geschlossen, Stahlblech, für Riemenriss ausgelegt | Eigenbau |
| 11 | Drehzahlgeber Bremse | Schenck-Geber nutzen oder Zahnrad + Näherungsschalter (Schlupf-/Riss-/Überdrehzahlerkennung) | – |

Auswuchten: Scheiben für > 30 m/s dynamisch wuchten lassen (Gütestufe G6.3 oder besser), bei
Bestellung angeben. Nach dem Aufbohren/Nachbearbeiten neu wuchten.

## Noch zu klären

- W130: Wellendurchmesser/Wellenende, Anschluss (Flansch/Gelenkwelle), Rotorträgheit, Kennlinie
- Rolle: Wellenende frei? Durchmesser? Lager für zusätzliche Riemenquerkraft (≈ 1–3 kN) geeignet?
- Platz: Achsabstand Rolle ↔ Bremse → Riemenlänge festlegen
- Soll der Prüfstand > 200 km/h können? Dann kleinere Scheibe auf der Rolle (z.B. Z96) und Neuauslegung

## Quellen

- norelem, Technischer Hinweis für Zahnriemen 22062 (Leistungstabellen HTD 8M, zul. Umfangskraft)
- Gates, Poly Chain GT Carbon Drive Design Manual (Grauguss-Scheiben max. 6500 ft/min ≈ 33 m/s)
- SDP/SI, Timing Belt Design Suggestions (Guss-Scheiben über 30 m/s wuchten)
- Mädler, riemen-profi.de, HolTech Antriebstechnik, TYMA: HTD-8M-Taper-Scheiben 22–192 Zähne, 20/30/50/85 mm
