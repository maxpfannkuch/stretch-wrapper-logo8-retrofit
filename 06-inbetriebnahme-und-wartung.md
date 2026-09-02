# 06 — Inbetriebnahme, Wartung und Störungssuche

---

## Grundregeln für gefahrloses Testen

**QF2 nicht ausschalten, um die Maschine stillzulegen.** Der Hilfskontakt des GV2ME05
liegt in der Sicherheitskette auf I9. Ist QF2 aus, startet der Zyklus gar nicht — man
sucht dann stundenlang einen Fehler, den es nicht gibt.

**Stattdessen die Ausgangsdrähte abklemmen:** 25 und 26 an Q3/Q4 sowie 21 und 23 an
Q6/Q7. Enden isoliert wegbinden. Dann kann nichts anlaufen, und die Sicherheitskette
bleibt intakt.

**Testprogramme ohne Ausgänge bauen.** Eingänge auf Merker legen, nie auf Q.

---

## Werkzeuge in Soft Comfort

| Werkzeug | Menü | Braucht passendes Programm im Gerät? |
|---|---|---|
| **E/A-Zustand** | Extras → Übertragen → E/A-Zustand | nein |
| **Online-Test** | Extras → Online-Test | ja |
| **Simulation** | Extras → Simulation | nein, läuft am PC |

**E/A-Zustand** ist das schnellste Werkzeug zum Identifizieren von Drähten. Es zeigt
alle Ein- und Ausgänge live, ohne dass ein bestimmtes Programm geladen sein muss.

**Online-Test** färbt Verbindungen im Plan ein: rot bedeutet 1, blau bedeutet 0.
Er verlangt, dass das Programm in der LOGO mit dem geöffneten übereinstimmt.

**Simulation** spielt das Programm am Rechner durch, ganz ohne Hardware.

---

## Erstinbetriebnahme in fünf Stufen

### Stufe 0 — Simulation am Rechner

Vorher die Parameter verkleinern, sonst muss man 64-mal klicken. **Originalwerte
notieren.**

| Baustein | normal | für die Simulation |
|---|---|---|
| B006 Zahnzaeler | 64 | 4 |
| B031, B032, B033 | 3 / 3 / 2 | 2 |
| B035 Z_schleich | 32 | 2 |
| B036 Z_stop | 64 | 3 |
| B040 Anlaufzeit | 45 s | 10 min |

Die Anlaufzeit hochsetzen ist wichtig: Beim langsamen Durchklicken würde die
Drehtellerüberwachung sonst eine Störung auslösen.

Eingangsarten in der Simulationsleiste per Rechtsklick festlegen:

| Eingang | Art |
|---|---|
| I3, I5, I9, I10, I13, I14 | Schalter |
| I4, I6, I12, F1–F4 | Taster |

Durchlauf:

| Schritt | Aktion | Erwartung |
|---|---|---|
| Grundstellung | I9 ein, I3 ein, I5 ein | nichts leuchtet |
| Start | I6 antippen | M1, M4, Q1, Q2, Q3 an |
| Bodenwicklungen | I4 achtmal antippen | Z_Unten zählt auf 2 |
| Übergang | — | M5 an, **Q7** an |
| Wagen verlässt Boden | I3 aus | Q7 bleibt an |
| Oberkante | 3 s warten, I5 aus | nach Filter + Überfahrt: M6 an, Q7 aus |
| Kopfwicklungen | I4 achtmal antippen | Z_Oben zählt auf 2 |
| Übergang | — | M7 an, **Q6** an |
| Unten | I3 ein | M20 an, Q6 aus |
| Abschluss | I4 achtmal antippen | Z_abschluss zählt auf 2 |
| Positionieren | — | M9 an |
| Schleichfahrt | I4 zweimal antippen | **Q4** an, **Q2 fällt ab** |
| Stop | I4 antippen | alles fällt ab, „Zyklus beendet" |

Nebenfunktionen prüfen:

- **Pause:** F1 mitten im Zyklus. Q3, Q6, Q7 fallen ab, Merker halten. F1 nochmal, es
  läuft weiter.
- **Stop:** I12. Alles fällt ab. Neustart beginnt von vorn, nicht in der Mitte.
- **Sicherheitskette:** I9 aus. Sofortiger Abbruch. I9 wieder ein — **nichts darf von
  allein anlaufen.**
- **Handbetrieb:** F2 gibt Q7, F3 gibt Q6, beide gleichzeitig gibt **nichts**.
- **Betriebsart 2:** Mittelstellung. Nach den Kopfwicklungen springt die Kette direkt
  zu M9, Q6 bleibt aus.
- **Betriebsart 3:** I14 ein. Q6 und Q7 bleiben den ganzen Zyklus aus.
- **Referenzfahrt:** Simulation neu starten mit I3 auf 0. Q6 muss kommen.

Danach alle Parameter zurücksetzen.

### Stufe 1 — Zahnzählung am Gerät verifizieren

Klebeband als Marke auf Teller und Rahmen. Zähler auf F4 zurücksetzen lassen, exakt
eine Umdrehung von Hand drehen, Wert im Meldetext ablesen. Zur Kontrolle fünf
Umdrehungen und durch fünf teilen.

**Ergebnis in B006 eintragen** — den gemessenen Wert, nicht den theoretischen.

### Stufe 2 — Schrittkette trocken

Alle Ausgangsdrähte ab. Programm übertragen, Online-Test starten.

Zahnimpulse durch Drehen des Tellers von Hand, Endschalter mit Magnet auslösen,
Lichtschranke mit der Hand abdecken.

**Wichtigste Prüfung:** Es darf immer nur **genau ein** Schrittmerker aus M4, M5, M6,
M7, M20, M9 aktiv sein. Sind zwei gleichzeitig rot, stimmt eine Reset-Verbindung nicht.
Das ist der häufigste Fehler in Schrittketten.

### Stufe 3 — Drehteller

Drähte 25 und 26 wieder anklemmen, QF1 ein. Teller leer räumen, Not-Halt in Reichweite.

Am Umrichter auf **d01** die Ausgangsfrequenz mitlesen. In Schritt 6 muss die
Umschaltung auf 15 Hz kommen.

Hier werden B035 und B036 eingestellt. Über drei bis vier Durchläufe messen, bevor
nachjustiert wird — einzelne Abweichungen kommen auch durch unterschiedliche
Palettengewichte.

### Stufe 4 — Wickelwagen

Drähte 21 und 23 anklemmen, QF2 ein.

**Zuerst nur Handbetrieb:** F2 muss aufwärts fahren, F3 abwärts. Stimmt das nicht, sind
Q6 und Q7 vertauscht — dann im Programm tauschen, nicht die Verdrahtung.

Danach vollständiger Zyklus mit Testpalette. Fahrzeiten messen und daraus B038 und B039
festlegen, etwa das Anderthalbfache der längsten gemessenen Fahrzeit.

### Stufe 5 — Störungen provozieren

Tür während des Zyklus öffnen, Stop drücken, Motorschutz auslösen, Not-Halt betätigen.

Nach jedem Ereignis muss die Maschine in einem definierten Zustand stehen und nach dem
Quittieren **nicht von selbst weiterlaufen**.

---

## Störungssuche

### Der Zähler zählt nicht

| Beobachtung | Ursache |
|---|---|
| Cnt bleibt auf 0 oder 1 | Reset liegt dauerhaft an — M1 am ZahnReset nicht invertiert |
| Cnt steigt, aber M3 kommt nie | Ein-Schwelle falsch (640 statt 64) |
| Cnt zählt rückwärts | Signal liegt auf Dir statt auf Cnt |
| Wicklungszähler zählt nicht | Der UND-Block davor bekommt M3 oder den Schrittmerker nicht |

### Die Maschine dreht endlos

Fast immer eine Folge des vorherigen Punkts. Ohne Umdrehungsimpuls zählt kein
Wicklungszähler, ohne Wicklungszähler kommt kein Übergang, die Kette bleibt in
Schritt 1 stehen.

### Der Zyklus startet nicht

| Prüfung | |
|---|---|
| Weiße Lampe an? | sonst Tür, Bodenkontakt |
| I9 auf 1? | sonst Motorschutz |
| I3 auf 1? | Wagen nicht unten oder Magnet außerhalb des Schaltfensters |
| M2 auf 0? | Störung anstehend, mit F4 quittieren |

### Der Wagen fährt nicht

Bei Handbetrieb F2/F3: Läuft ein Zyklus? Dann ist der Handbetrieb gesperrt.
Ist die entsprechende Endlage schon erreicht?

Im Automatikbetrieb: Ist Pause aktiv? Ist der richtige Schrittmerker gesetzt?

### Palette bleibt im falschen Winkel stehen

| Beobachtung | Maßnahme |
|---|---|
| bleibt zu früh stehen | B036 Z_stop erhöhen |
| dreht zu weit | B036 Z_stop verringern |
| Winkel ändert sich mit der Bremskraft | prüfen, ob B068 Bremse Frei korrekt verdrahtet ist |
| Winkel driftet über viele Zyklen | verlorene Zahnimpulse, siehe unten |

Ein Zahnimpuls kann verlorengehen, wenn er genau in den Zyklus fällt, in dem der
Zahnzähler zurückgesetzt wird. Bei einer Zykluszeit von wenigen Millisekunden gegen
78 ms Zahnabstand liegt das im Promillebereich. Bei erkennbarer Drift wäre ein
zusätzlicher Referenzsensor am Teller die saubere Lösung.

### Lichtschranke meldet dauerhaft

Empfindlichkeit zu weit aufgedreht, sodass der Sensor die Umgebung sieht — oder der
Ausgangstransistor ist defekt. Poti komplett zurückdrehen und prüfen. Auch prüfen, ob
versehentlich Pin 2 statt Pin 4 verdrahtet ist; der invertierte Ausgang wäre bei freier
Sicht dauerhaft an.

**Notbehelf:** Während des Zyklus mit **F2** die Oberkante von Hand markieren. Diese
Funktion ist im Programm dauerhaft vorhanden.

### Datum blinkt am Display

Die Uhr ist nicht gestellt. Für dieses Programm ohne Bedeutung, weil keine Zeitfunktion
verwendet wird. Stellen über das Gerätemenü oder Extras → Übertragen → Uhrzeit stellen.

### Umrichter reagiert nicht auf einen Ausgang

Am Umrichter **d05** aufrufen und den Ausgang schalten. Zeigt d05 keine Reaktion, ist
das Problem in der Verdrahtung oder beim Massebezug, nicht im Programm.

---

## Wartung

### Regelmäßig

| Intervall | Tätigkeit |
|---|---|
| monatlich | Lichttaster-Optik reinigen |
| monatlich | Sitz des Zahnradsensors und Abstand prüfen |
| halbjährlich | Endschalter und Bodenkontakt auf Funktion prüfen |
| halbjährlich | Bremsbelag und Luftspalt der Folienbremse prüfen |
| jährlich | Schützkontakte KM1/KM2 sichtprüfen |
| jährlich | Not-Halt und Türschalter auf Wirksamkeit prüfen |

### Wenn die Folienspannung nachlässt

Zuerst B065 Bremskraft am Display erhöhen. Bringt auch 100 Prozent nicht genug Zug,
liegt es an der Bremse selbst — Belag verschlissen oder Luftspalt zu groß.

### Programm sichern

Vor jeder Änderung das aktuelle Programm aus der LOGO hochladen und archivieren.
Die am TDE eingestellten Werte sind Teil davon.

---

## Offene Punkte

Bewusst nicht umgesetzt, aber dokumentiert:

**Obere Endlage nur softwareseitig.** Die Fahrzeitüberwachung fängt einen ausgefallenen
Reedkontakt ab, ersetzt aber keinen zwangsöffnenden Endschalter im Kreis von KM2.
Ein solcher Schalter wäre die richtige Lösung.

**Keine Folienrisserkennung.** Reißt die Folie, wickelt die Maschine bis zum Zyklusende
weiter. Nachrüstbar mit Tastrolle oder Lichtschranke am Folienwagen auf einen freien
Eingang.

**Keine Wiederanlaufsperre im Umrichter.** Siehe
[03-frequenzumrichter.md](03-frequenzumrichter.md).

**Kein zweiter Hilfskontakt des Motorschutzschalters.** Die Steuerung kann Motorschutz
und Türöffnung nicht unterscheiden.

**Wagengeschwindigkeit nicht einstellbar.** Der Hubmotor läuft mit fester Drehzahl am
Netz. Für eine unabhängige Einstellung wäre ein zweiter kleiner Frequenzumrichter nötig
— bei geschätzt 0,25 kW Motorleistung eine überschaubare Investition. Vom Takten der
Schütze ist wegen des Verschleißes abzuraten.

**Freilaufdiode an der Bremse.** Würde die PWM-Ansteuerung bei 10 Hz spürbar glätten.
