# 02 — Elektrik und Signale

## Adressierung verstehen

Die LOGO zählt über Grundgerät und Erweiterung durch. Das ist die häufigste
Fehlerquelle beim Lesen von Plänen:

| Physisch | Im Programm |
|---|---|
| Grundgerät I1–I8 | I1–I8 |
| DM16 Eingang 1–8 | **I9–I16** |
| Grundgerät Q1–Q4 | Q1–Q4 |
| DM16 Ausgang 1–8 | **Q5–Q12** |

Der Motorschutzschalter hängt also physisch am ersten Eingang des Erweiterungsmoduls,
heißt im Programm aber I9.

---

## Eingänge

| Adresse | Draht | Signal | Ruhezustand |
|---|---|---|---|
| I1 | — | Poti stillgelegt | — |
| I2 | — | Poti stillgelegt | — |
| I3 | 17 | Endschalter unten (Magnet) | 0, Schließer |
| I4 | 18 | **Zahnradsensor Drehteller** | wechselnd |
| I5 | 3 | SICK-Lichttaster Palettenhöhe | 1 bei erkannter Palette |
| I6 | 19 | Start (grüner Leuchttaster) | 0, Schließer |
| I7 | — | Poti stillgelegt | — |
| I8 | — | frei | — |
| I9 | 15 | **Sicherheitskette / Freigabe** | 1 |
| I10 | 16 | Endschalter oben (Magnet) | 0, Schließer |
| I11 | — | frei | — |
| I12 | 20 | Stop (roter Leuchttaster) | 0, Schließer |
| I13 | — | Drehschalter Stellung 1 | 0 |
| I14 | — | Drehschalter Stellung 3 | 0 |
| I15 | — | frei | — |
| I16 | — | frei | — |

I4 liegt bewusst auf einem der schnellen Zähleingänge I3 bis I6.

---

## Ausgänge

| Adresse | Draht | Signal | Typ |
|---|---|---|---|
| Q1 | 32 | Leuchttaster grün | Transistor 0,3 A |
| Q2 | 33 | Folienbremse (PWM) | Transistor 0,3 A |
| Q3 | 25 | FU Klemme 1 — Start Rechtslauf | Transistor 0,3 A |
| Q4 | 26 | FU Klemme 3 — Festfrequenz CF1 | Transistor 0,3 A |
| Q5 | 31 | frei | Relais 5 A |
| Q6 | 21 | **Wickelwagen ab** | Relais 5 A |
| Q7 | 23 | **Wickelwagen auf** | Relais 5 A |
| Q8–Q12 | — | frei | Relais 5 A |

---

## Die Schützverriegelung — Achtung, Überkreuz

Das ist die Stelle, an der man sich am leichtesten vertut.

Gemessener Aufbau:

- **KM2 Klemme 22 → KM1 Klemme A1**
- **KM1 Klemme 22 → KM2 Klemme A1**

Eine klassische gegenseitige Verriegelung über die Hilfsöffner. Die LOGO speist nicht
die Spule direkt, sondern die Klemme 21 des jeweils anderen Schützes. Daraus folgt:

| Ausgang | speist Klemme 21 von | zieht an | Bewegung |
|---|---|---|---|
| Q6 (Draht 21) | KM2 | **KM1** | **ab** |
| Q7 (Draht 23) | KM1 | **KM2** | **auf** |

Die Drahtnummern legen also die falsche Zuordnung nahe. Prüfe das an deiner Anlage
immer selbst nach, bevor du das erste Mal einen Zyklus fahren lässt.

**Drei unabhängige Verriegelungsebenen sind wirksam:**

1. Mechanische Verriegelung der beiden Schütze (blauer Bügel)
2. Elektrische Verriegelung über die Hilfsöffner
3. Softwareverriegelung im Programm (B050 Auf Frei und B053 Ab Frei)

---

## Die Sicherheitskette

In Reihe geschaltet, mit hardwareseitiger Selbsthaltung:

```
  Motorschutzschalter GV2ME05
            |
     Bodenkontakt Bernstein (Öffner)
            |
       Türschalter Wickelkopf (Öffner)
            |
      Selbsthaltung mit weißem Taster
            |
      +-----+-----+
      |           |
   I9 an LOGO   Freigabe KM1/KM2 und Umrichter
```

**Der weiße Taster ist die Wiedereinschalttaste.** Er kann nur zurücksetzen, wenn die
Kette wieder geschlossen ist. Bei offener Tür bleibt die Lampe deshalb dunkel, egal wie
oft man drückt.

Die Kette schaltet die Aktoren **hardwareseitig** ab. Das Programm sieht den Zustand
nur über I9 und bricht den Zyklus ab.

### Diagnose-Eigenheit

Die weiße Lampe hängt an einem anderen Punkt der Kette als I9:

| Beobachtung | Ursache |
|---|---|
| Lampe aus, I9 = 0 | Tür oder Bodenkontakt hat ausgelöst |
| Lampe an, I9 = 0 | Motorschutzschalter ausgelöst |

**Die LOGO kann diesen Unterschied nicht sehen.** Wer eine unterscheidbare
Klartextmeldung am TDE will, legt einen zweiten Hilfskontakt des GV2ME05 auf einen
freien Eingang — I8, I11, I15 oder I16 sind frei.

### Typische Verklemmung

Löst der Bodenkontakt aus, ist I9 auf 0 und die Schütze sind stromlos. Um freizukommen,
müsste der Wagen hochfahren — genau das ist aber gesperrt. Der Wagen muss dann
**mechanisch** angehoben werden, bis der Rollenhebel frei ist.

Die Sicherheitskette dabei **nicht überbrücken**, auch nicht kurz.

---

## Not-Halt

Der Not-Halt nimmt sowohl den Umrichter als auch KM1 und KM2 stromlos. Die LOGO bleibt
unter Spannung und läuft weiter.

**Konsequenz:** Die Steuerung erkennt den Not-Halt nur indirekt über den Ausfall der
Umdrehungsimpulse (Drehtellerüberwachung nach 40 s) beziehungsweise über die
Fahrzeitüberwachung.

**Empfohlene Ergänzung:** Die Wiederanlaufsperre USP des Umrichters auf die freie
Klemme 4 legen. Sie verhindert, dass der Teller nach dem Entriegeln sofort anläuft,
solange der Startbefehl von Q3 noch ansteht.

---

## Nicht verwendete Reserven

| Ressource | Verwendbar für |
|---|---|
| I8, I11, I15, I16 | Folienriss, zweiter Motorschutz-Kontakt, FU-Rückmeldung |
| Q5, Q8–Q12 | FU-Reset, Signalleuchte, Hupe |
| FU Klemme 2 (RV) | Linkslauf, hier nicht benötigt |
| FU Klemme 4 (CF2) | zweite Festfrequenz oder USP |
| FU Klemme 5 (RS) | Reset vom Bedienpult aus |
| FU Klemme 11 und 12 | Rückmeldung RUN oder Störung an die LOGO |
| I1, I2, I7 | Analogeingänge AI3, AI4, AI1 |
