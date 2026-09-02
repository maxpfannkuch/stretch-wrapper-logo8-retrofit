# 04 — Das Schaltprogramm

Zielgerät: **LOGO! 8.FS4**, Sprache FUP, rund 90 Bausteine von 400 möglichen.

---

## Wie das Programm aufgebaut ist

Das Programm ist eine **Schrittkette**: Sechs Selbsthalterelais, von denen immer genau
eines aktiv ist. Weitergeschaltet wird über Übergangsbedingungen, die jeweils prüfen,
ob der aktuelle Schritt läuft und seine Bedingung erfüllt ist.

```
Start + Wagen unten + Freigabe
              |
              v
   +-------------------------+
   | S1  Wicklungen unten    |  M4
   +-------------------------+
              | Z_Unten erreicht
              v
   +-------------------------+
   | S2  Wagen aufwärts      |  M5
   +-------------------------+
              | Lichtschranke frei + Ueberfahrt
              | oder Endschalter oben
              | oder F2 gedrückt
              v
   +-------------------------+
   | S3  Wicklungen oben     |  M6
   +-------------------------+
              | Z_Oben erreicht
              v
   +-------------------------+
   | S4  Wagen abwärts       |  M7
   +-------------------------+
              | Endschalter unten
              v
   +-------------------------+
   | S5  Wicklungen Abschluss|  M20
   +-------------------------+
              | Z_abschluss erreicht
              v
   +-------------------------+
   | S6  Positionieren       |  M9
   |  Zahn 32 -> Schleichfahrt|
   |  Zahn 64 -> Teller aus  |
   +-------------------------+
              | Auslauf
              v
      Palette in Ausgangslage
```

### Die Positionierung

Der Zahnzähler B006 zählt 64 Impulse und erzeugt daraus einen Umdrehungsimpuls M3.
Weil Schritt 6 exakt auf einem solchen Impuls beginnt, startet er immer in der
Ausgangsposition der Palette.

Innerhalb dieser letzten Umdrehung zählen zwei weitere Zähler die Zähne:

- **B035 Z_schleich** schaltet bei Zahn 32 die Festfrequenz von 15 Hz ein
- **B036 Z_stop** beendet bei Zahn 64 den Zyklus

Gleichzeitig mit der Schleichfahrt wird die **Folienbremse geöffnet**. Dadurch wird der
Auslauf unabhängig von der eingestellten Folienspannung — sonst würde jede Änderung der
Bremskraft den Endwinkel verschieben.

### Die drei Betriebsarten

| Drehschalter | Eingang | Ablauf |
|---|---|---|
| Stellung 1 | I13 = 1 | S1 → S2 → S3 → S4 → S5 → S6 |
| Stellung 2 (Mitte) | beide 0 | S1 → S2 → S3 → S6 |
| Stellung 3 | I14 = 1 | S1 → S6 |

In allen Fällen wird Schritt 6 durchlaufen, die Palette ist also immer ausgerichtet.

Realisiert über zwei zusätzliche Übergänge B075 T1b und B076 T3b, die zusammen mit
B024 T5 über den ODER-Block B074 auf M17 führen.

---

## Merkerbelegung

| Merker | Bedeutung |
|---|---|
| M1 | Zyklus aktiv |
| M2 | Störung |
| M3 | Umdrehungsimpuls, 1 Zyklus lang |
| M4 | Schritt 1 — Wicklungen unten |
| M5 | Schritt 2 — Wagen aufwärts |
| M6 | Schritt 3 — Wicklungen oben |
| M7 | Schritt 4 — Wagen abwärts |
| M9 | Schritt 6 — Positionieren |
| M10 | Palette erkannt, gefiltert |
| M11 | Pause aktiv |
| M12 | Startimpuls |
| M13–M17 | Übergänge |
| M18 | Zyklus beendet |
| M20 | Schritt 5 — Wicklungen Abschluss |
| M21 | Referenzfahrt läuft |
| M23 | Betriebsart „nur hoch" |
| M26 | Displaybeleuchtung TDE |

**Nicht verwendbar:** M8 ist der Anlaufmerker, M25 bis M31 steuern Display und
Zeichensatz. M26 wird hier bewusst für die Beleuchtung genutzt.

---

## Bausteinliste

Die Blocknummern entsprechen dem aktuellen Stand. Sie können sich beim Einfügen neuer
Bausteine verschieben — maßgeblich sind die **Namen**.

### Zyklussteuerung

| Nr | Name | Typ | Eingänge | Ausgang |
|---|---|---|---|---|
| B001 | Startbeding | UND | I6, I9, I3, M2 (inv) | → B003 S |
| B002 | Abbruchbedi | ODER | I12, I9 (inv), M2, M18 | → B003 R |
| B003 | Zyklus | Selbsthalterelais | — | M1 |
| B004 | Startimpuls | Flankenwischrelais | Trg = M1, T = 0,20 s | M12 |

### Zahnzählung

| Nr | Name | Typ | Eingänge / Parameter | Ausgang |
|---|---|---|---|---|
| B005 | ZahnReset | ODER | M3, M1 (inv) | → B006 R |
| B006 | Zahnzaeler | Vor-/Rückwärtszähler | Cnt = **I4 direkt**, On = 64, Off = 1 | M3 |
| B007 | LS_Filter | Ausschaltverzögerung | Trg = I5, T = 0,50 s | M10 |

Der Zähleingang muss **direkt** auf I4 liegen, sonst greift die schnelle Zählfunktion
nicht. Die Rückführung des Ausgangs auf den Reset läuft über den Merker M3, weil eine
direkte Schleife als Fehler gemeldet würde.

Aus-Schwelle 1 statt 0: Bei 0 würde der Ausgang nach dem Reset gesetzt bleiben, weil
die Bedingung „Zählwert kleiner als Aus-Schwelle" bei Zählwert 0 nicht erfüllt ist.

### Schrittkette

| Nr | Name | Typ | S | R | Ausgang |
|---|---|---|---|---|---|
| B012 | S1_Boden | Selbsthalterelais | M12 | B014 | M4 |
| B008 | S2_Auf | Selbsthalterelais | M13 | B015 | M5 |
| B009 | S3_Kopf | Selbsthalterelais | M14 | B019 | M6 |
| B010 | S4_Ab | Selbsthalterelais | M15 | B018 | M7 |
| B011 | S5_Abschluss | Selbsthalterelais | M16 | B017 | M20 |
| B013 | S6_Position | Selbsthalterelais | M17 | B016 | M9 |

Die Reset-Blöcke B014 bis B019 sind ODER-Gatter aus dem jeweils nächsten Übergang und
M1 invertiert. Damit fällt die ganze Kette ab, sobald der Zyklus endet.

### Übergänge

| Nr | Name | Typ | Eingänge | Ausgang |
|---|---|---|---|---|
| B020 | T1 | UND | M4, B031, I14 (inv) | M13 |
| B026 | T2a | ODER | M10 (inv), I10, B064 | → B067 |
| B067 | Ueberfahrt | Einschaltverzögerung | Trg = B026, T = 4,50 s | → B021 |
| B027 | MinFahrzeit | Einschaltverzögerung | Trg = M5, T = 3,00 s | → B021 |
| B021 | T2 | UND | M5, B027, B067 | M14 |
| B022 | T3 | UND | M6, B032, M23 (inv) | M15 |
| B023 | T4 | UND | M7, I3 | M16 |
| B024 | T5 | UND | M20, B033 | → B074 |
| B025 | T6 | UND | M9, B036 | M18 |
| B064 | TasteOben | UND | F2, M5 | → B026 |
| B073 | M_NurHoch | UND | I13 (inv), I14 (inv) | M23 |
| B075 | T1b | UND | M4, B031, I14 | → B074 |
| B076 | T3b | UND | M6, B032, M23 | → B074 |
| B074 | OR_M17 | ODER | B024, B075, B076 | M17 |

`MinFahrzeit` verhindert, dass Schritt 2 sofort wieder verlassen wird, wenn der
Lichttaster schon in unterster Stellung frei ist.

`Ueberfahrt` lässt den Wagen nach dem Freiwerden der Lichtschranke noch weiterfahren,
damit die Folie über die Palettenoberkante hinaus gelegt wird.

`TasteOben` erlaubt es, die Oberkante während des Zyklus mit F2 von Hand zu markieren —
nützlich bei defektem Lichttaster.

### Wicklungszähler

| Nr | Name | Typ | Cnt | R | On |
|---|---|---|---|---|---|
| B028 | Cnt_unten | UND | M3, M4 | — | — |
| B029 | Cnt_oben | UND | M3, M6 | — | — |
| B030 | CNT_Abschluss | UND | M3, M20 | — | — |
| B031 | Z_Unten | Zähler | B028 | M1 (inv) | 3 |
| B032 | Z_Oben | Zähler | B029 | M1 (inv) | 3 |
| B033 | Z_abschluss | Zähler | B030 | M1 (inv) | 2 |

Drei getrennte Zähler statt eines umgeschalteten. Kostet ein paar Bausteine mehr, spart
aber jede Referenzierungs- und Rechenlogik: Jede Phase hat ihre eigene, direkt am
Display einstellbare Schwelle.

### Positionierung

| Nr | Name | Typ | Cnt | R | On |
|---|---|---|---|---|---|
| B034 | Cnt_position | UND | I4, M9 | — | — |
| B035 | Z_schleich | Zähler | B034 | M9 (inv) | 32 |
| B036 | Z_stop | Zähler | B034 | M9 (inv) | 64 |

### Pause

| Nr | Name | Typ | Eingänge | Ausgang |
|---|---|---|---|---|
| B037 | Pause | Stromstoßrelais | Trg = F1, R = M1 (inv) | M11 |

F1 schaltet die Pause abwechselnd ein und aus. Am Zyklusende wird sie gelöscht.
Schrittkette und Zähler behalten ihren Stand.

### Referenzfahrt

| Nr | Name | Typ | Eingänge / Parameter | Ausgang |
|---|---|---|---|---|
| B072 | RefFahrt | Selbsthalterelais | S = M8, R = B070 | M21 |
| B070 | RefReset | ODER | I3, B069 | → B072 R |
| B069 | RefTimeout | Einschaltverzögerung | Trg = M21, T = 90 s | → B043, → B084 |
| B071 | RefAb | UND | M21, I3 (inv), I9 | → B055 |

M8 ist genau im ersten Programmzyklus nach dem Einschalten auf 1. Steht der Wagen schon
unten, setzt das Selbsthalterelais gar nicht, weil beim RS der Reset Vorrang hat.

### Überwachung und Störung

| Nr | Name | Typ | Eingänge / Parameter | Ausgang |
|---|---|---|---|---|
| B038 | ZeitAuf | Einschaltverzögerung | Trg = B048, T = 90 s | → B043, → B060 |
| B039 | ZeitAb | Einschaltverzögerung | Trg = B051, T = 90 s | → B043, → B082 |
| B040 | Anlaufzeit | Einschaltverzögerung | Trg = B046, T = 45 s | → B042 |
| B041 | ImpulsWaeche | Ausschaltverzögerung | Trg = M3, T = 40 s | → B042 |
| B042 | TellerFehler | UND | B040, B041 (inv) | → B043, → B083 |
| B043 | StoerSet | ODER | B038, B039, B042, B069 | → B045 S |
| B044 | StoerQuit | ODER | I12, F4 | → B045 R |
| B045 | Stoerung | Selbsthalterelais | — | M2 |

Die Trigger hängen bewusst an den **tatsächlichen Fahrbefehlen** B046, B048 und B051,
nicht an den Schrittmerkern. Sonst würde eine Pause die Überwachungszeit weiterlaufen
lassen und einen Fehlalarm auslösen.

Die 40 s des Impulswächters müssen deutlich über der langsamsten Umdrehungszeit von
20 s liegen.

### Ausgangslogik

| Nr | Name | Typ | Eingänge | Ausgang |
|---|---|---|---|---|
| B046 | Teller | UND | M1, I9, M2 (inv), M11 (inv) | Q3 |
| B047 | Schleichfahr | UND | M9, B035 | Q4 |
| B048 | AufAuto | UND | M5, M1, M11 (inv), I10 (inv) | → B054 |
| B049 | AufHand | UND | F2, M1 (inv), I10 (inv) | → B054 |
| B054 | Auf | ODER | B048, B049 | → B050, → B053 (inv) |
| B050 | Auf Frei | UND | B054, B055 (inv), I9 | Q7 |
| B051 | AbAuto | UND | M7, M1, M11 (inv), I3 (inv) | → B055 |
| B052 | AbHand | UND | F3, M1 (inv), I3 (inv) | → B055 |
| B055 | Ab | ODER | B051, B052, B071 | → B053, → B050 (inv) |
| B053 | AbFrei | UND | B055, B054 (inv), I9 | Q6 |
| B056 | Blinker | Asynchroner Impulsgeber | En = M2, 0,5 s / 0,5 s | → B057 |
| B057 | Lampe | ODER | M1, B056 | Q1 |

Die Verriegelung greift auf die Signale **vor** der Verriegelung zu — dadurch entsteht
keine Schleife und beide Ausgänge können nie gleichzeitig aktiv werden.

### Folienbremse

| Nr | Name | Typ | Eingänge / Parameter | Ausgang |
|---|---|---|---|---|
| B065 | Bremskraft | Analog-Arithmetik | En = hi, V1 = 55, V2–V4 = 0 | → B066 Ax |
| B068 | Bremse Frei | UND | M1, B047 (inv) | → B066 En |
| B066 | BremsePWM | Impulsbreitenmodulator | Periode 0,10 s, Min 0, Max 100 | Q2 |

Die Analog-Arithmetik dient hier als **Konstantengeber**, weil die LOGO keinen
einfachen Analogkonstanten-Baustein hat. V1 ist der am Display einstellbare Sollwert
in Prozent.

`Bremse Frei` öffnet die Bremse, sobald die Schleichfahrt einsetzt.

### Displaybeleuchtung

| Nr | Name | Typ | Eingänge / Parameter | Ausgang |
|---|---|---|---|---|
| B085 | Taste1 | ODER | F1, F2, F3, F4 | → B087 |
| B086 | Taste2 | ODER | I6, I12, M2, M8 | → B087 |
| B087 | Lichttrigger | ODER | B085, B086, M1 | → B088 |
| B088 | Lichtnachlauf | Ausschaltverzögerung | T = 2 min | M26 |

Beleuchtung geht beim Einschalten an, jeder Tastendruck setzt die Zeit zurück, während
eines Zyklus bleibt sie dauerhaft an, zwei Minuten nach dem letzten Ereignis geht sie aus.

### Meldetexte

Siehe [05-parameter-und-bedienung.md](05-parameter-und-bedienung.md).

---

## Warum manches so und nicht anders gelöst ist

**Startbedingung verlangt I3.** Ohne Referenzpunkt weiß die Steuerung nicht, wo der
Wagen steht. Steht er oben, fährt der Bediener ihn mit F3 herunter — oder die
Referenzfahrt erledigt es beim nächsten Einschalten.

**Positionieren als eigener Schritt.** Nach den Abschlusswicklungen kommt genau eine
zusätzliche Umdrehung. Dadurch beginnt die Feinpositionierung garantiert am Nullpunkt,
ohne dass irgendwo gerechnet werden muss.

**Keine Remanenz.** Alle Zähler und Selbsthalterelais haben Remanenz aus. Nach einem
Spannungsausfall startet die Maschine im Grundzustand und macht nicht mitten im Zyklus
weiter.

**Reset dominiert.** Beim Selbsthalterelais der LOGO hat der Rücksetzeingang Vorrang.
Darauf beruht die Sicherheit des Abbruchs: Solange I9 fehlt, kann der Zyklus nicht
gesetzt werden.
