# Checkliste Prüfstand – 30.09.2026

Zum Ausdrucken und Ausfüllen. Werte in die Lücken `____` eintragen, Fotos mit der Nummer benennen
(z.B. `F7_W130_Typenschild.jpg`) und danach in `docs/wsb_schenck_w130/` bzw. `docs/messung_30-09/` ablegen.

---

## A. Anforderungen festlegen (Entscheidungen)

| # | Frage | Antwort |
|---|---|---|
| A1 | Welche Fahrzeuge sollen auf den Prüfstand? (Roller, Vespa, Motorrad, Auto?) | alle bis 250km/h |
| A2 | Höchste Fahrzeugleistung, die gemessen werden soll [PS] | ___200kw_ |
| A3 | Höchstgeschwindigkeit am Prüfstand [km/h] (> 200 km/h? → andere Riemenscheibe) | ____ |
| A4 | Kleinste Geschwindigkeit für Dauerlast mit Bremse [km/h] | ____ |
| A5 | Messarten mit WSB: konst. Drehzahl / konst. km/h / Rampe / Lastpunkte / Straßenlast | __1-4__ |
| A6 | Sicherer Zustand bei Fehler: Bremse aus (Rolle frei) oder Bremse halten? | __frei__ |
| A7 | Soll der Trägheitsmodus (ohne Bremse) weiter genutzt werden? | ja / nein |
| A8 | Moment direkt messen (Pendel + Kraftmessdose) gewünscht? | ja  |
| A9 | rusEFI-Kanäle, die wir brauchen (TPS, λ, MAP, Zündwinkel, Einspritzzeit, IAT, CLT, …) | __alle sollen möglich sein__ |
| A10 | Rechner am Prüfstand: Mac / Raspberry Pi / beides? Bildschirm für Fahrer? | ___wildschirm für fahrer gerne pi aber egal linux oder mac_ |
| A11 | Budget-Rahmen Mechanik (Riementrieb, Lager, Kupplung, Haube) [€] | __1000__ |

---

## B. Vergleichstest (Details: `PyST/TESTPLAN.md`)

- [ ] **B1** Alte Firmware sichern: `st-flash --connect-under-reset read ~/pst_firmware_backup_alt.bin 0x08000000 0x80000`
- [ ] **B2** Test A: alte Firmware + alte LabVIEW, 3 Läufe, XML gespeichert
- [ ] **B3** Neue Firmware flashen (`pio run -t upload`), `python3 tools/pst_monitor.py` → Version 1.0.0 sichtbar
- [ ] **B4** Test B1/B2: neue Firmware + LabVIEW (3.2.1 / alte), Filter bei 60 Hz: **MA 105 / dq 45**, 3 Läufe
- [ ] **B5** Test C: neue Firmware + PyST am Mac, 3 Läufe
- [ ] **B6** Gleiche Einstellungen überall: 100 Inkr/U, 1,57 m, J 13,5, i fest ____, n vom Gas ____ (über Haltedrehzahl!)

| Test | Lauf 1 PS @ 1/min | Lauf 2 | Lauf 3 | COM-Fehler | Bemerkung |
|---|---|---|---|---|---|
| A (alt/alt) | ____ | ____ | ____ | ____ | ____ |
| B1 (neu/3.2.1) | ____ | ____ | ____ | ____ | ____ |
| B2 (neu/alt) | ____ | ____ | ____ | ____ | ____ |
| C (neu/PyST) | ____ | ____ | ____ | ____ | ____ |

Klima zu Beginn: ____ °C, ____ mbar, ____ %   Ende: ____ °C, ____ mbar

---

## C. Zündsignal

| # | Messung | Ergebnis |
|---|---|---|
| C1 | Wie wird abgegriffen? (Zange am Zündkabel / Primärseite / Geber / anderes) | _zündkabel______ |
| C2 | Neue FW Standard, ~3000 1/min: Motor (Zündung) / Motor (Rolle × i) | ____ / ____ |
| C3 | Neue FW Standard, ~6000 1/min: Motor (Zündung) / Motor (Rolle × i) | ____ / ____ |
| C4 | `v`-Ausgabe: Impulse Zündung / verworfen | ____ / ____ |
| C5 | FW **dp70**, ~3000 1/min: Zündung / Rolle × i | ____ / ____ |
| C6 | FW **dp70**, ~6000 1/min: Zündung / Rolle × i | ____ / ____ |
| C7 | `v` (dp70): Impulse / verworfen → Anteil Störimpulse = verworfen ÷ Impulse | ____ % |
| C8 | Eingänge getauscht (Zündung an PB9): Fehler wandert mit? | ja / nein |
| C9 | Abgriff/Leitung verändert (Abstand, Schirm, Masse): Anteil Störimpulse | ____ % |

- [ ] **F1** Oszilloskop vor der Eingangsschaltung (eine Umdrehung sichtbar)
- [ ] **F2** Oszilloskop an PA0 nach Optokoppler, gleiche Zeitbasis
- [ ] **F3** Foto Abgriff + Leitungsführung

---

## D. Schenck W130 – Daten und Maße

- [ ] **F4** Typenschild Bremse (gut lesbar)
- [ ] **F5** Bremse gesamt von vorne, hinten, oben
- [ ] **F6** Wellenende(n) nah, mit Maßstab
- [ ] **F7** Pendellagerung, Hebelarm, Kraftmessdose (falls vorhanden)

| # | Größe | Wert |
|---|---|---|
| D1 | Typ / Seriennummer / Baujahr | ____ |
| D2 | Nennleistung / max. Moment / max. Drehzahl (Typenschild) | ____ kW / ____ Nm / ____ 1/min |
| D3 | Wellenende: Ø ____ mm, Länge ____ mm, Passfeder ____ × ____ mm, Gewinde/Flansch ____ |
| D4 | Wellenmitte über Grundplatte / Boden | ____ mm |
| D5 | Befestigung: Lochbild Fuß/Grundrahmen (Abstände, Schraubengröße) | ____ |
| D6 | Pendel: vorhanden? Hebelarmlänge (Mitte Welle → Kraftmessdose) | ____ mm |
| D7 | Kraftmessdose: Typ, Messbereich, Ausgang (mV/V, 0–10 V, …) | ____ |
| D8 | Eigener Drehzahlgeber an der Bremse? Typ / Zähne / Ausgang | ____ |
| D9 | Gewicht (Typenschild/geschätzt) | ____ kg |
| D10 | Vorhandene Kupplung/Gelenkwelle: Typ, Länge, Flansch | ____ |

---

## E. Rolle und Aufbau (für den Riementrieb)

- [ ] **F8** Rolle mit Lagerung und Wellenenden (beide Seiten)
- [ ] **F9** Draufsicht Rolle + Bremse + freier Platz dazwischen (mit Zollstock im Bild)

| # | Größe | Wert |
|---|---|---|
| E1 | Rollen-Ø / Rollenbreite | ____ mm / ____ mm |
| E2 | Wellenende Rolle frei? Seite links/rechts, Ø ____ mm, Länge ____ mm, Passfeder ____ |
| E3 | Rollenlager: Typ/Bezeichnung (steht oft auf dem Gehäuse, z.B. UCP 210) | ____ |
| E4 | Abstand Rollenwelle ↔ Bremsenwelle heute (Mitte–Mitte), horizontal / vertikal | ____ / ____ mm |
| E5 | Wellen parallel? Höhenunterschied der Wellenmitten | ____ mm |
| E6 | Freier Bauraum für Vorgelegewelle + Stehlager (L × B × H) | ____ |
| E7 | Größte Riemenscheibe, die neben die Rolle passt (Ø) | ____ mm |
| E8 | Rahmen: Material/Profil, Befestigungsmöglichkeit für Lagerbock | ____ |
| E9 | Rollengeber: Typ, Inkremente/U, Ausgang (Push-Pull, NPN, RS-422), Spur A/B? | ____ |

---

## F. Endstufe / Erregergerät

- [ ] **F10** Typenschild Endstufe
- [ ] **F11** Klemmleisten (alle, lesbar), Stecker
- [ ] **F12** Innenaufbau (Platinen, Thyristoren, Trafo)
- [ ] **F13** Alle Schaltplanseiten (scannen/fotografieren)

| # | Frage | Antwort |
|---|---|---|
| F1a | Hersteller / Typ der Endstufe | ____ |
| F2a | Netzanschluss (230 V / 400 V, Absicherung) | ____ |
| F3a | Ausgang zur Spule: max. Strom / Spannung | ____ A / ____ V |
| F4a | Sollwert-Eingang vorhanden? (0–10 V / 4–20 mA / Poti) – Klemmen | ____ |
| F5a | Freigabe / Not-Aus-Eingang – Klemmen | ____ |
| F6a | Istwert-Ausgang (Strom), Störmeldung – Klemmen | ____ |
| F7a | Eingebaute Überwachungen (Wasser, Temperatur, Drehzahl)? | ____ |
| F8a | Spulenwiderstand (mit Multimeter, **stromlos!**) an den Spulenklemmen | ____ Ω |
| F9a | Funktioniert die Endstufe noch? (nur prüfen, wenn sicher möglich) | ja / nein / nicht geprüft |

---

## G. Kühlwasser

- [ ] **F14** Wasseranschlüsse Bremse, Wächter, Leitungen

| # | Frage | Antwort |
|---|---|---|
| G1 | Anschluss: Leitungswasser / Kreislauf mit Pumpe + Kühler? | ____ |
| G2 | Anschlussgröße Zulauf / Ablauf | ____ / ____ |
| G3 | Vorhandene Wächter: Durchfluss / Temperatur / Druck (Typ, Schaltpunkt) | ____ |
| G4 | Durchfluss (Eimer + Stoppuhr: Liter in 30 s × 2) | ____ l/min |

---

## H. Sensoren und Sonstiges

| # | Frage | Antwort |
|---|---|---|
| H1 | EGT: Thermoelement angeschlossen? Typ K? Welcher Kanal? | ____ |
| H2 | Lambda: vorhandener Controller (Typ, Ausgang analog/CAN) | ____ |
| H3 | rusEFI vorhanden? Board-Typ, CAN verdrahtet? | ____ |
| H4 | 12-V-Versorgung am Prüfstand vorhanden? | ja / nein |
| H5 | Netzwerk/WLAN am Prüfstand? | ja / nein |
| H6 | Weitere Wünsche/Ideen aus dem Test | ____ |

---

## Mitbringen / ablegen

- [ ] Alle LabVIEW-XML und PyST-Ordner → `docs/messung_30-09/`
- [ ] Fotos F1–F14 → `docs/messung_30-09/` bzw. W130/Endstufe → `docs/wsb_schenck_w130/`
- [ ] Schaltplan-Scans → `docs/wsb_schenck_w130/`
- [ ] Diese Liste ausgefüllt (Foto oder abgetippt)
