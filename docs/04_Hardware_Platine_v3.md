# Schritt 4 – Neue Messplatine (v3)

Konzept, noch kein Schaltplan. Ziel: störfeste Drehzahl-Eingänge, USB direkt am Mikrocontroller,
CAN für rusEFI und Sensormodule, Vorbereitung für die WSB (Schritt 5).

## Mikrocontroller

| | STM32F446 (heute) | **STM32G474 (Vorschlag)** | STM32H723 (Option) |
|---|---|---|---|
| Takt | 180 MHz | 170 MHz | 550 MHz |
| Komparatoren / DAC | – / 2 | **7 / 7** | 2 / 2 |
| CAN | 2× CAN 2.0 | **3× FDCAN** | 3× FDCAN |
| USB | FS | FS | HS/FS |
| ADC | 3× 2,4 MS/s | 5× 4 MS/s | 3× (16 bit) |
| Besonderes | – | HRTIM 184 ps (WSB-PWM), OPAMPs | Ethernet |
| Board zum Start | Nucleo-F446RE | **Nucleo-G474RE** | Nucleo-H723ZG |

G474: Komparatoren + DAC erlauben die adaptive Triggerschwelle für Zündung/VR-Geber direkt im Chip,
HRTIM ist ideal für den WSB-Strom, 3× FDCAN = getrennte Busse für rusEFI und Prüfstandsmodule.

## Blockschaltbild

```
                 ┌────────────────────────── Messplatine v3 ──────────────────────────┐
Zündung (Zange) ─┤ Schutz → Tiefpass → COMP (Schwelle aus DAC) → Totzeit → TIM-Capture │
                 │                  └→ ADC (Oszilloskop-Modus)                          │
Kurbel-Geber VR ─┤ MAX9926 (adaptiv, Nulldurchgang) ───────────────────→ TIM-Capture   │
Kurbel-Geber Hall┤ Schmitt-Trigger ────────────────────────────────────→ TIM-Capture   │
Rolle A/B (422) ─┤ AM26LV32 → TIM Encoder-Modus + Capture                               │
Reserve 2× DI   ─┤ Optokoppler → TIM-Capture (z.B. 2. Rolle, Hinterrad)                 │
EGT 1–4         ─┤ 4× MAX31856 (SPI)                                                    │
Analog 4×       ─┤ 0–5 V, Teiler + RC, ADC (λ-Controller, TPS, MAP …)                    │
Klima           ─┤ BME280 (I2C)                                                         │
Kraftmessdose   ─┤ ADS131M04 oder ADS1220 (SPI), Brückenspeisung                         │
                 │                                                                      │
USB (isoliert)  ─┤ ADuM4160 + isolierter DC/DC ── USB-FS des STM32 (CDC, DTR)            │
CAN 1 (rusEFI)  ─┤ Transceiver, 500 kbit/s, Terminierung schaltbar                       │
CAN 2 (Module)  ─┤ Transceiver, 1 Mbit/s / CAN-FD                                        │
Not-Aus         ─┤ Eingang (Öffner) → Hardware-Sperre WSB + Meldung an MCU               │
WSB-Schnittstelle┤ PWM/Freigabe/Fehler (zum Leistungsteil, Schritt 5) oder über CAN 2    │
Status          ─┤ LEDs, Summer, 12-V-Versorgung mit Verpolschutz                         │
                 └──────────────────────────────────────────────────────────────────────┘
```

## Zündsignal-Aufbereitung (Kern des Problems)

1. **Schutz:** Serienwiderstand, TVS, Klemmdioden – Zangensignale haben Spitzen von vielen Volt.
2. **Tiefpass** (einige µs) gegen HF-Anteile des Funkens.
3. **Komparator mit Hysterese**, Schwelle vom DAC: Firmware misst die Spitzenhöhe (ADC) und setzt die
   Schwelle auf ~50 % → funktioniert mit schwachen und starken Signalen.
4. **Totzeit in Hardware** (Komparator-Blanking des G474 oder Monoflop): nach einem Impuls für einstellbar
   1–3 ms blind → Nachschwingen/Überschwinger gar nicht erst sichtbar.
5. **Plausibilitätsfilter** in der Firmware (Schritt 2.2) als zweite Stufe.
6. **ADC-Abgriff** des aufbereiteten Signals für den Oszilloskop-Modus.

Beste Quelle bleibt der **Kurbelwellengeber** (wie ECU) oder die **Drehzahl von rusEFI über CAN**.

## Störfestigkeit

- USB galvanisch getrennt (Zündstörungen kommen sonst über die Masse bis in den PC).
- Geschirmte, verdrillte Leitungen; Schirm einseitig; differenzielle Signale wo möglich (Rolle RS-422, CAN).
- Sternpunkt-Masse, getrennte Masseflächen analog/digital, Eingänge mit TVS.
- Steckverbinder verriegelnd (Vibration).

## Stückliste – Kandidaten

| Funktion | Bauteil |
|---|---|
| MCU-Board | Nucleo-G474RE (später eigene Platine mit STM32G474RET6) |
| USB-Isolation | ADuM4160 + B0505S (1 W isoliert) |
| CAN-Transceiver | TCAN1042 / SN65HVD230 (3,3 V) |
| VR-Geber | MAX9926 (2 Kanäle) |
| Encoder-Empfänger | AM26LV32 |
| Thermoelemente | MAX31856 × 4 |
| Kraftmessdose | ADS131M04 (4 kS/s, simultan) oder ADS1220 |
| Klima | BME280 |
| Optokoppler | 6N137 / ACPL-M61L |

## Offene Fragen

- Welche Geber sind am Prüfstand vorhanden (Rolle: Strichzahl, Ausgang; Motor: Zange/Geber)?
- 12-V-Versorgung vom Prüfstand oder USB-Versorgung?
- Gehäuse und Stecker (M12, Deutsch DT, Sub-D)?
- Eigene Platine sofort oder erst Aufsteckplatine für den Nucleo-G474RE (empfohlen als erster Schritt)?
