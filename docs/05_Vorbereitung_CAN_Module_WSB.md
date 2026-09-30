# Schritt 5 – Vorbereitung: CAN-Sensormodule und WSB-Leistungsteil

Die Module und das WSB-Leistungsteil werden ein **eigenes Projekt**. Hier werden die Schnittstellen
festgelegt, damit Messplatine v3 (Schritt 4), Firmware und PyST schon passend gebaut werden.

## Busaufteilung

| Bus | Teilnehmer | Bitrate | Grund |
|---|---|---|---|
| **CAN 1** | Messplatine ↔ rusEFI | 500 kbit/s (rusEFI-Standard) | rusEFI-Broadcast (Basis-ID 0x200) unverändert nutzen, keine ID-Konflikte |
| **CAN 2** | Messplatine ↔ PST-Module (EGT, λ, Kraft, WSB, Anzeige) | 1 Mbit/s (später CAN-FD) | eigener ID-Raum, deterministische Last, WSB-Regelung |

Die Messplatine ist Zeitbasis und Sammelstelle: Sie stempelt alle CAN-Nachrichten beim Empfang und
leitet sie als Kanäle an den PC weiter (Protokoll v2).

## ID-Plan CAN 2 (11 bit)

| ID-Bereich | Inhalt |
|---|---|
| 0x010 | Messplatine: Zeit/Synchronisation (Zeitstempel µs, 10 Hz) |
| 0x020 | Messplatine: Modus, Sollwerte an WSB (100 Hz) |
| 0x021 | Messplatine: Heartbeat / Freigabe (100 Hz) – Ausbleiben > 50 ms → WSB sicher |
| 0x100 + n·0x10 | Modul n (n = 0…15): Daten, bis 16 IDs je Modul |
| 0x700 + n | Modul n: Heartbeat/Status (10 Hz) |
| 0x7E0 / 0x7E8 | Konfiguration/Update (Anfrage/Antwort) |

Vorgesehene Module:

| n | Modul | Daten |
|---|---|---|
| 0 | WSB-Schnittstelle zur Schenck-Endstufe | Sollwert, Freigabe, Strom-Istwert, Wasser (Durchfluss/Temperatur), Störmeldungen |
| 1 | EGT-Modul (4–8× MAX31856) | Temperaturen, Fehler je Kanal |
| 2 | λ-Controller (Fremdgerät mit CAN, z.B. Spartan 3/AEM X) | λ, Status – ggf. Umsetzung auf fremde IDs |
| 3 | Kraftmessdose | Kraft [N], Status |
| 4 | Fahrer-Anzeige | Tasten (Start/Abbruch), Anzeige-Befehle |

Ein **DBC-File** (`pst_can2.dbc`) wird die verbindliche Beschreibung; PyST liest es und legt die
Kanäle automatisch an.

## Die Bremse: Schenck W130

Vorhanden: Schenck **W130** (Wirbelstrombremse, wassergekühlt) mit den **alten Endstufen und Schaltplänen**.
Sie bremst die Rolle. Unterlagen bitte nach [`wsb_schenck_w130/`](wsb_schenck_w130/) legen (PDF/Fotos von
Schaltplänen, Typenschild, Kennlinienblatt, Endstufe innen/außen).

**Übliche Kenndaten der Baureihe – am Typenschild/Datenblatt prüfen:**

| Größe | Richtwert W130 | Bemerkung |
|---|---|---|
| Leistung | 130 kW | Dauerleistung bei ausreichender Kühlung |
| Drehmoment | ~400 Nm | Maximum, drehzahlabhängig (s.u.) |
| Drehzahl | 10 000 1/min max. | eigene Angabe; Überdrehzahlschutz vorsehen |
| Kühlung | Wasser | Durchfluss, Druck, Austrittstemperatur überwachen |
| Aufbau | pendelnd gelagert mit Hebelarm | Moment direkt messbar (Kraftmessdose), falls vorhanden |

**Was daraus folgt:**

1. **Endstufe weiterverwenden statt neu bauen.** Schenck-Erregergeräte arbeiten meist mit Thyristor-
   Phasenanschnitt am Netz und eigenem Stromregler und erwarten einen Sollwert (typisch 0–10 V oder
   0/4–20 mA) plus Freigabe; sie liefern oft Strom-Istwert und Störmeldung zurück. Dann braucht unsere
   Seite nur: **Sollwert-Ausgang (galvanisch getrennt), Freigabe, Istwert-Eingang, Störung-Eingang** – die
   Leistungselektronik bleibt Schenck. → Schaltpläne prüfen: Sollwert-Schnittstelle, Freigabe/Not-Aus-Kette,
   interne Überwachungen (Wasser, Temperatur, Drehzahl).
2. **Wasserkühlung ist sicherheitskritisch.** Ohne Durchfluss wird die Bremse in Sekunden überhitzt.
   Pflicht: Durchflusswächter, Austrittstemperatur, ggf. Druck – in Hardware mit der Freigabe verriegelt
   (Erregung nur bei Wasser OK), zusätzlich als Kanäle an die Messplatine.
3. **Kopplung Rolle ↔ Bremse klären.** Direkt auf der Rollenwelle (Rolle heute bis ~1700 1/min) oder über
   Übersetzung? Davon hängen Überdrehzahl-Grenze und nutzbares Bremsmoment ab.
4. **Moment bei kleiner Drehzahl.** Wirbelstrombremsen bringen bei niedriger Drehzahl wenig Moment
   (Kennlinie steigt mit der Drehzahl, bis die Erregung begrenzt). Beispiel: 32 PS (≈ 24 kW) bei 1500 1/min
   Rolle ≈ 150 Nm an der Rollenwelle → mit der Kennlinie der W130 bei dieser Drehzahl vergleichen.
5. **Trägheit und Verluste ändern sich.** Der Rotor der W130 erhöht die Rollenträgheit J, Luft-/Wasser-
   reibung erhöht das Verlustmoment → nach dem Einbau J neu bestimmen und Verlustmoment per Auslaufversuch
   (M0/M1) ermitteln, auch im Trägheitsmodus mit abgeschalteter Erregung.
6. **Momentmessung:** Ist die Bremse pendelnd gelagert und eine Kraftmessdose vorhanden, bekommen wir das
   Moment direkt (M = F · Hebelarm) – dann Kraftmessdosen-Eingang (24-bit ADC) auf Platine v3 einplanen.

## WSB – Anforderungen und Sicherheitskonzept (Entwurf)

**Regelung (im Mikrocontroller, nie im PC):**
- Erregerstrom-Regelung: in der vorhandenen Schenck-Endstufe (falls deren Stromregler brauchbar ist);
  nur falls nicht: eigenes Leistungsteil (PWM 5–20 kHz, Regeltakt ≥ 1 kHz)
- Drehzahl-/Geschwindigkeitsregler: 100–1000 Hz (Messplatine, Istwert Rolle, Stellgröße Strom-Soll über CAN 2)
- Modi: konstante Motordrehzahl, konstante Geschwindigkeit, Rampe (1/min/s), Lastpunkt (Strom/Moment), Straßenlast

**Sicherheit (in Hardware abgesichert):**
- Not-Aus-Taster (Öffner) → sperrt den Gate-Treiber direkt, unabhängig von Software
- Heartbeat-Ausfall (CAN 2) > 50 ms → Strom auf definierten Zustand
- Übertemperatur Spule/Kühlkörper → Strom begrenzen/abschalten
- Überdrehzahl Rolle → Meldung, ggf. Zündunterbrechung (Ausgang vorsehen)
- Watchdog in beiden Mikrocontrollern
- Freilauf/Energieabbau der Spule bei Abschaltung (Induktivität!)

**Offene Entscheidung – sicherer Zustand:** Strom aus (Rolle läuft frei, Fahrer muss Gas wegnehmen) oder
definierte Bremsung? Hängt von Bremse und Aufbau ab → im WSB-Projekt festlegen.

## Was wir vorab brauchen

- [x] Bremse: **Schenck W130**, bremst die Rolle, max. ~10 000 1/min
- [ ] Typenschild + Kennlinienblatt (Moment/Leistung über Drehzahl) der W130
- [ ] Schaltpläne der alten Endstufe(n): Sollwert-Eingang, Freigabe, Istwert, Störmeldungen, Not-Aus-Kette
- [ ] Typ/Bezeichnung der Endstufe (Erregergerät), Netzanschluss (230/400 V), Nennerregerstrom
- [ ] Spulendaten (Widerstand, Induktivität, Nennstrom/-spannung) – steht meist im Schaltplan/Typenschild
- [ ] Kühlwasser: vorhandene Wächter (Durchfluss, Temperatur, Druck), Anschluss, Durchfluss l/min
- [ ] Kopplung Rolle ↔ Bremse (direkt / Riemen, Übersetzung)
- [ ] Pendellagerung + Kraftmessdose vorhanden? Typ, Messbereich, Hebelarm
- [ ] Welche rusEFI-Kanäle wollen wir (TPS, λ, MAP, Zündwinkel, Einspritzzeit, IAT, CLT)?

## Vorbereitung in den bestehenden Projekten

| Wo | Was |
|---|---|
| Messplatine v3 (Schritt 4) | CAN 1 + CAN 2, Not-Aus-Eingang, WSB-Schnittstelle, 12 V |
| Protokoll v2 (Schritt 3) | Kanäle aus CAN-Quellen, `SET_MODE`/`SETPOINT`, `HEARTBEAT` |
| PyST | Kanäle generisch (fertig), später Reiter „Bremse“ (Modus, Sollwert, Istwerte), DBC-Import |
