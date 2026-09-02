# 03 — Frequenzumrichter Hitachi L100

## Wichtig vorab

Der L100 verwendet **zweistellige** Funktionscodes: F01, A01, C01, d05.
Die neueren Baureihen X200, L200 und WJ200 verwenden dreistellige Codes.
Wer eine Anleitung für die falsche Baureihe erwischt, sucht die Parameter vergeblich.

---

## Ausgelesene Parameter der Referenzmaschine

| Parameter | Wert | Bedeutung |
|---|---|---|
| F01 | 33,5 Hz | Sollwert Bedienfeld — bei A01 = 01 wirkungslos |
| F02 | 6,5 s | Beschleunigungszeit, bezogen auf die Maximalfrequenz |
| F03 | 3,5 s | Verzögerungszeit, bezogen auf die Maximalfrequenz |
| A01 | 01 | Frequenzsollwert von der **Steuerklemme** (Analogeingang O) |
| A02 | 01 | Startbefehl von der **Steuerklemme** |
| A03 | 50 Hz | Basisfrequenz |
| A04 | 60 Hz | Maximalfrequenz |
| A11 | 0 | Skalierung Analogeingang, Anfangsfrequenz |
| A12 | 0 | Skalierung Analogeingang, Endfrequenz |
| A13 | 0 % | Skalierung Analogeingang, Anfangsspannung |
| A14 | 100 % | Skalierung Analogeingang, Endspannung |
| A15 | 01 | Startfrequenz-Option |
| A16 | 8 | Filter Analogeingang |
| A20 | 60 | Festfrequenz 0 |
| A21 | **15 Hz** | Festfrequenz 1 — Schleichfahrt zum Positionieren |
| A22 | 0,0 | Festfrequenz 2, unbelegt |
| C01 | 00 | Klemme 1 = FW (Rechtslauf) |
| C02 | 01 | Klemme 2 = RV (Linkslauf), unbelegt |
| C03 | 02 | Klemme 3 = CF1 (Festfrequenz Bit 0) |
| C04 | 03 | Klemme 4 = CF2, unbelegt |
| C05 | 18 | Klemme 5 = RS (Reset), unbelegt |
| C11 | 0 | Eingang 1 als Schließer |

Die Gruppe A11 bis A16 skaliert den Analogeingang. Wenn die Drehzahlspreizung des
Potentiometers geändert werden soll, ist das die richtige Stelle.

---

## Klemmenbelegung

```
  Obere Reihe:   L   5   4   3   2   1   P24
  Untere Reihe:  H   O   OI  L   FM  CM2 12  11
```

| Klemme | Funktion | Belegung in der Referenzmaschine |
|---|---|---|
| P24 | interne 24-V-Quelle | gemeinsamer Bezug der Eingänge |
| 1 | FW Rechtslauf | **von Q3** (Draht 25) |
| 2 | RV Linkslauf | frei |
| 3 | CF1 Festfrequenz | **von Q4** (Draht 26) |
| 4 | CF2 | frei |
| 5 | RS Reset | frei |
| H | 10-V-Referenz | Drehzahl-Potentiometer |
| O | Analogsollwert 0–10 V | Schleifer des Potentiometers |
| L | Masse | Potentiometer |
| 11, 12 | programmierbare Ausgänge | frei |

**Warnung:** H und L niemals kurzschließen, das zerstört die interne Referenz.

Das Drehzahl-Potentiometer gibt stufenlos 15 bis 60 Hz vor und bleibt bewusst erhalten
— es arbeitet direkt am Umrichter, ohne Beteiligung der Steuerung.

---

## Bedienung des Umrichters

- **FUNC** steigt ins Menü ein
- **Pfeiltasten** navigieren zwischen den Gruppen d, F, A, b, C
- **FUNC** zeigt den Wert eines Parameters
- **STR speichert** — nur drücken, wenn eine Änderung gewollt ist
- **ESC** geht zurück

Lassen sich Parameter nicht ändern, ist der Schreibschutz **b31** aktiv.

**Achtung:** Die Initialisierung liegt auf **b84**. Dort keinesfalls versehentlich
auslösen — sonst sind alle Einstellungen weg.

---

## Diagnose ohne dass sich etwas dreht

| Code | Anzeige |
|---|---|
| d01 | Ausgangsfrequenz |
| d02 | Motorstrom |
| d03 | Drehrichtung |
| **d05** | **Signalzustand der Digitaleingänge** |
| d06 | Signalzustand der Ausgänge |

**d05 ist das wichtigste Werkzeug beim Retrofit.** Damit prüfst du, ob ein LOGO-Ausgang
den Umrichter tatsächlich erreicht, ohne dass der Motor läuft. Motorschutz aus,
Umrichter auf d05 stellen, LOGO-Ausgang schalten, Anzeige beobachten.

Das klärt in fünf Minuten die Frage, an der Retrofits sonst tagelang hängen: ob die
Logikrichtung und der Massebezug stimmen.

### Zum Massebezug

Die Digitaleingänge des Umrichters beziehen sich auf dessen eigene P24-Klemme. Ein
plusschaltender Transistorausgang der LOGO funktioniert nur, wenn

- der Umrichter auf Positiv-Logik steht **und**
- die 0-V-Masse der LOGO mit Klemme L des Umrichters verbunden ist.

In der Referenzmaschine ist beides gegeben, die Transistorausgänge funktionieren direkt.

**Sauberer wäre**, dafür potentialfreie Relaisausgänge zu verwenden und einfach P24 auf
die jeweilige Eingangsklemme durchzuschalten. Das funktioniert unabhängig von Jumper
und Massebezug. Bei einem Neuaufbau würde ich diesen Weg wählen.

---

## Nachlaufberechnung

Die Verzögerungszeit F03 bezieht sich auf die Maximalfrequenz:

```
Bremszeit = F03 × (aktuelle Frequenz ÷ A04)
```

Für die Referenzmaschine mit F03 = 3,5 s und A04 = 60 Hz:

| Ausgangszustand | Drehzahl | Bremszeit | Nachlaufweg |
|---|---|---|---|
| aus 60 Hz | 12 min⁻¹ | 3,5 s | ca. 0,35 Umdr. = **22 Zähne** |
| aus 15 Hz | 3 min⁻¹ | 0,9 s | ca. 0,02 Umdr. = **1–2 Zähne** |

Die Schleichfahrt reduziert den Nachlauf um etwa den Faktor 15. Das ist der Grund,
warum die Festfrequenz von 15 Hz auf CF1 überhaupt existiert — der ursprüngliche
Konstrukteur hat das genau so vorgesehen.

### Warum die Positionierung über den Drehzahlbereich funktioniert

Das Herunterbremsen von 60 auf 15 Hz dauert

```
3,5 s × (60 − 15) ÷ 60 = 2,6 Sekunden
```

und kostet bei einer mittleren Drehzahl von 7,5 min⁻¹ etwa 21 Zähne.

Läuft der Teller langsamer, wird die Bremsphase kürzer und die Schleichfahrt länger —
der Endpunkt bleibt gleich. Deshalb braucht es keine drehzahlabhängigen Werte.

---

## Empfohlene Änderungen

**A04 auf 50 Hz reduzieren.** Oberhalb der Basisfrequenz von 50 Hz läuft der Motor in
der Feldschwächung und verliert Drehmoment. Bei schweren Paletten kann das knapp werden.

**Klemme 5 (Reset) belegen.** Ein freier Relaisausgang der LOGO dorthin, und eine
Umrichterstörung lässt sich vom Bedienpult aus quittieren.

**Wiederanlaufsperre USP auf Klemme 4.** Verhindert den sofortigen Anlauf nach dem
Entriegeln des Not-Halts.

**Rückmeldung an die LOGO.** Klemme 11 oder 12 über C21 beziehungsweise C22 auf „RUN"
oder „Störung" parametrieren und auf einen freien LOGO-Eingang legen. Dann weiß die
Steuerung, ob der Teller tatsächlich dreht, statt es nur aus dem Ausbleiben der
Zahnimpulse zu schließen.

**Gleichstrombremsung als Alternative zur Schleichfahrt.** Der L100 kann den Motor am
Ende aktiv festhalten statt auslaufen zu lassen. Klemme 4 auf die Funktion DB legen,
Bremskraft und Bremsdauer in der A5x-Gruppe einstellen. Damit wird der Stopppunkt
nahezu lastunabhängig. Für die Referenzmaschine nicht nötig, weil die Bremse beim
Positionieren ohnehin geöffnet wird.
