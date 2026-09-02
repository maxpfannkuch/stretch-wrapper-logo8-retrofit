# 08 — Nachbau auf einer anderen Maschine

Dieses Kapitel richtet sich an alle, die einen ähnlichen Palettenwickler auf LOGO!
umbauen wollen. Die Referenzmaschine ist ein H. Böhm Typ 2020, aber das Konzept passt
auf die meisten Drehteller-Wickler mit Wendeschützschaltung und Frequenzumrichter.

---

## Was du mindestens brauchst

### Hardware

| Komponente | Anforderung |
|---|---|
| LOGO! Grundgerät | **12/24 V DC oder 24 V DC** — wegen der schnellen Zähleingänge |
| Erweiterungsmodul | DM8 oder DM16, je nach Anzahl der Signale |
| Display | TDE empfohlen; ohne wird die Parametrierung mühsam |
| Netzteil | 24 V DC, ausreichend für Schütze und Sensorik |

**Die 230-V-Variante der LOGO ist ungeeignet.** Ihre Eingänge schaffen nur wenige Hertz
und verschlucken die Impulse des Drehtellersensors.

### Signale

Diese acht Signale sind das Minimum:

| Signal | Typ |
|---|---|
| Start | Taster |
| Stop | Taster |
| Drehtellersensor | Impulsgeber, auf einen schnellen Eingang |
| Endschalter unten | Schalter |
| Endschalter oben | Schalter |
| Höhenerkennung | Lichttaster |
| Sicherheitsfreigabe | Rückmeldung der Sicherheitskette |
| Wickelwagen auf / ab | zwei Ausgänge |
| Umrichter Start | ein Ausgang |

Optional, aber sehr empfehlenswert: Umrichter-Festfrequenz für die Schleichfahrt,
Folienbremse, Signalleuchte.

---

## Schritt 1 — Bestandsaufnahme

**Fotografiere alles.** Typenschilder, Klemmenleisten, Bedientableau, jedes Bauteil.
Bei einer zwanzig Jahre alten Maschine gibt es keine Unterlagen mehr, und die
Drahtnummern der alten Steuerung sind oft die einzige Spur.

**Nimm die Drahtnummern auf.** Sie hängen an den Adern und verraten die
ursprüngliche Zuordnung. Verlass dich aber nicht blind darauf — in der Referenzmaschine
führen die Nummern bei den Schützen in die Irre.

**Lies die Umrichterparameter aus, bevor du irgendetwas änderst.** Besonders:

- Sollwertquelle und Startquelle
- Basisfrequenz und Maximalfrequenz
- alle Festfrequenzen
- Funktionszuordnung der Digitaleingänge
- Beschleunigungs- und Verzögerungszeit

Die Verzögerungszeit brauchst du später für die Nachlaufberechnung.

**Verstehe die Sicherheitskette.** Was schaltet der Not-Halt ab? Was passiert bei
offener Tür? Gibt es eine Selbsthaltung und wie wird sie zurückgesetzt? Diese Kette
bleibt in Hardware und wird nicht ins Programm verlagert.

---

## Schritt 2 — Signale identifizieren

Klemme die LOGO an, aber lass alle Ausgänge ab. Programm übertragen, dann
**Extras → Übertragen → E/A-Zustand**.

Jetzt jeden Taster einzeln drücken, jeden Sensor auslösen, jeden Endschalter betätigen
und mitschreiben, welcher Eingang reagiert.

Dabei gleich festhalten:

- Ist der Kontakt ein **Öffner oder Schließer**? Also: Ist der Eingang in Ruhe 1 oder 0?
- Bei Sensoren: **PNP oder NPN**? PNP passt direkt auf die LOGO-Eingänge.

Endschalter sollten idealerweise Öffner sein — dann führt ein Drahtbruch zum sicheren
Zustand. Sind es Schließer, notiere das als bekannte Schwachstelle und ergänze eine
Fahrzeitüberwachung.

---

## Schritt 3 — Fahrtrichtungen bestimmen

**Nicht raten.** Bei einer Wendeschützschaltung mit gegenseitiger Verriegelung speist
ein Ausgang oft nicht die Spule des Schützes, dessen Nummer er trägt, sondern die des
anderen.

Vorgehen: Klemme 22 jedes Schützes verfolgen. Führt sie zur Spule A1 des anderen
Schützes, hast du eine Überkreuz-Zuordnung.

Endgültig geklärt wird das im Handbetrieb: Erst einen Ausgang schalten, schauen wohin
der Wagen fährt, dann beschriften.

---

## Schritt 4 — Zähne messen

Setz einen einfachen Zähler auf den Drehtellersensor und lass dir den Stand in einem
Meldetext anzeigen. Klebeband als Marke auf Teller und Rahmen, fünf Umdrehungen fahren,
durch fünf teilen.

Der gemessene Wert ist der, den du einträgst — nicht der theoretisch berechnete.

Prüf gleichzeitig die maximale Impulsfrequenz:

```
Impulse pro Sekunde = Zähne × Umdrehungen pro Minute ÷ 60
```

Bis 5 kHz ist alles unkritisch. Bei mehreren hundert Hertz solltest du sicherstellen,
dass der Sensor unmittelbar am Cnt-Eingang des Zählers hängt.

---

## Schritt 5 — Nachlauf berechnen

```
Bremszeit = Verzögerungszeit × (aktuelle Frequenz ÷ Maximalfrequenz)

Nachlaufweg in Umdrehungen = 0,5 × Drehzahl in Umdr/s × Bremszeit
```

Rechne das für die höchste und die niedrigste Arbeitsfrequenz durch.

Ist der Nachlauf bei voller Drehzahl größer als etwa fünf Zähne, brauchst du eine
Schleichfahrt. Dafür konfigurierst du eine Festfrequenz im Umrichter und legst einen
LOGO-Ausgang auf den entsprechenden Digitaleingang.

**Faustregel für den Umschaltpunkt:**

```
Umschaltzahn = Stoppzahn − Zähne für das Herunterbremsen − Reserve
```

Die Zähne für das Herunterbremsen ergeben sich aus der Bremszeit zwischen Arbeits- und
Schleichfrequenz, multipliziert mit der mittleren Drehzahl in diesem Bereich.

Als Reserve mindestens vier bis fünf Zähne echte Schleichfahrt einplanen.

---

## Schritt 6 — Programm aufbauen

Nimm die Struktur aus [04-programm.md](04-programm.md) als Vorlage. Der Aufbau in
sechs Schritten passt auf praktisch jeden Palettenwickler.

Anzupassen sind:

- Anzahl und Reihenfolge der Schritte, falls dein Ablauf abweicht
- Übergangsbedingungen entsprechend deiner Sensorik
- Zähnezahl und Positionierwerte
- Betriebsarten, falls dein Bedientableau andere Wahlmöglichkeiten bietet

Nicht anpassen, sondern übernehmen:

- Die Zwei-Zähler-Kaskade für den Umdrehungsimpuls
- Die Verriegelungslogik der Fahrausgänge
- Die globale Freigabe über den Sicherheitskettenmerker
- Die Fahrzeit- und Drehtellerüberwachung
- Remanenz überall aus

---

## Schritt 7 — Gestuft in Betrieb nehmen

Die fünf Stufen aus [06-inbetriebnahme-und-wartung.md](06-inbetriebnahme-und-wartung.md)
haben sich bewährt: Simulation, Zahnzählung, Schrittkette trocken, Drehteller, Wagen.

Der wichtigste Grundsatz: **Erst wenn eine Stufe sauber läuft, kommt die nächste dazu.**
Wer alle Ausgänge gleichzeitig anklemmt, sucht Fehler an fünf Stellen gleichzeitig.

---

## Typische Stolpersteine

| Problem | Ursache |
|---|---|
| Zyklus startet nicht | Motorschutzschalter liegt in der Sicherheitskette — nicht ausschalten zum Testen |
| Wagen fährt in die falsche Richtung | Überkreuz-Verriegelung der Schütze |
| Zähler zählt nicht | Reset liegt dauerhaft an, meist eine fehlende Invertierung |
| Umrichter reagiert nicht | Massebezug zwischen LOGO und Umrichter fehlt |
| Palette bleibt im falschen Winkel | Stoppzahn nicht eingefahren, oder Bremse zieht während des Auslaufs |
| Endloses Drehen | Umdrehungsimpuls kommt nicht an |
| Maschine startet nach Not-Halt sofort | Wiederanlaufsperre im Umrichter fehlt |

---

## Was du besser machen kannst als die Referenzmaschine

Diese Punkte sind in der Referenzanlage bewusst oder aus Platzgründen nicht umgesetzt.
Bei einem Neuaufbau lohnen sie sich:

**Potentialfreie Relaisausgänge für den Umrichter.** Einfach P24 auf die jeweilige
Eingangsklemme durchschalten. Funktioniert unabhängig von Logikrichtung und Massebezug.

**Zwangsöffnender Endschalter in der oberen Endlage**, direkt im Kreis des
Aufwärtsschützes.

**Analogausgangsmodul für die Folienbremse** statt eines externen PWM-Moduls, wenn der
Platz in der Hutschienenreihe reicht.

**Rückmeldung vom Umrichter** auf einen freien Eingang, damit die Steuerung weiß, ob
der Teller tatsächlich dreht.

**Folienrisserkennung** am Folienwagen.

**Zweiter Hilfskontakt des Motorschutzschalters** für unterscheidbare Störmeldungen.

**Zweiter Frequenzumrichter für den Hubmotor**, wenn der Folienüberlappungsgrad
unabhängig von der Tellerdrehzahl einstellbar sein soll.

**Referenzsensor am Drehteller** zusätzlich zum Zahnradsensor, falls die Positionierung
über viele Zyklen wegdriftet.
