# 07 — LOGO! 8 Praxiswissen

Was beim Aufbau dieses Projekts an Zeit gekostet hat und in keinem Handbuch an einer
Stelle zusammensteht.

---

## Gerät und Projekt

### Die Bestellnummer richtig lesen

Siemens hat mit der LOGO! 8.2 die Nummerierung umgestellt. Die Generation steckt
**vor** dem Bindestrich, nicht dahinter:

| Bestellnummer | Gerät | Auswahl in Soft Comfort |
|---|---|---|
| 6ED1052-xxx00-0BA8 | LOGO! 8.0 / 8.1 | 0BA8.Standard |
| 6ED1052-xxx**08**-0BA0 | LOGO! 8.2 | **8.FS4** |
| 6ED1052-xxx08-0BA1 | LOGO! 8.3 | 8.FS5 |
| 6ED1052-xxx08-0BA2 | LOGO! 8.4 | 8.FS6 |

Falscher Hardwaretyp führt beim Online-Test zur Meldung „Der Hardwaretyp von LOGO! in
LOGO!Soft Comfort stimmt nicht mit dem des angeschlossenen LOGO! Geräts überein."

Am schnellsten geht es über **Extras → Übertragen → LOGO! erkennen** — die Software
liest Typ und Firmware direkt aus.

### Adressierung mit Erweiterungsmodul

Die LOGO zählt durch. DM16-Eingang 1 heißt im Programm **I9**, DM16-Ausgang 1 heißt
**Q5**. Das Erweiterungsmodul muss in der Hardwarekonfiguration eingetragen sein,
sonst sind diese Adressen gar nicht verfügbar.

### Module lassen sich nicht in eine zweite Reihe setzen

LOGO-Erweiterungen werden ausschließlich seitlich über den Busstecker angereiht. Ein
Verlängerungskabel gibt es nicht. Wenn der Platz in der Hutschienenreihe nicht reicht,
ist eine Erweiterung nicht möglich — dann müssen externe Baugruppen ran.

---

## Zeichnen und Verdrahten

### Jede Klemme existiert genau einmal

Das ist die größte Umgewöhnung gegenüber anderen Systemen: **Ein Eingang, ein Ausgang
und ein Merker können nur einmal aufs Blatt gelegt werden.** Im Auswahldialog sind
bereits vergebene Klemmen ausgegraut.

Wird ein Signal an mehreren Stellen gebraucht, zieht man vom **Ausgangspin mehrere
Leitungen** weg. Ein Pin verträgt beliebig viele abgehende Verbindungen.

Praktischer Hinweis: Das Verbindungswerkzeug springt nach jeder Leitung in den
Auswahlmodus zurück. Ein Doppelklick auf das Werkzeugsymbol hält es aktiv.

### Ein Merker hat genau einen Eingang

Daraus folgt: **Nur ein Baustein darf auf einen Merker schreiben.** Sollen mehrere
Quellen denselben Merker setzen, fasst man sie vorher über einen ODER-Block zusammen.

Wer einen Merker nicht mehr beschreiben kann, hat dort meist eine alte Verbindung
hängen, die zuerst gelöscht werden muss.

### Merker sind Bequemlichkeit, keine Pflicht

Ein Signal, das nur an ein oder zwei benachbarten Stellen gebraucht wird, verbindet man
besser direkt. Merker lohnen sich bei Signalen, die über den ganzen Plan verteilt
gelesen werden — in diesem Projekt etwa M1 mit rund einem Dutzend Lesestellen.

### Reservierte Merker

| Merker | Bedeutung |
|---|---|
| **M8** | Anlaufmerker — nur im ersten Zyklus nach dem Start auf 1 |
| **M25 bis M31** | Displaybeleuchtung, Zeichensatz, Meldetextsteuerung |

M8 als Schrittspeicher zu verwenden funktioniert nicht — der Editor markiert den Block
farbig. Umgekehrt ist M8 der ideale Trigger für Referenzfahrt und Startbild.

M26 und M27 steuern die beiden Farbkanäle der TDE-Beleuchtung. Damit lässt sich
zwischen Weiß, Bernstein und Rot umschalten — praktisch für eine Störungsanzeige, die
quer durch die Halle sichtbar ist.

### Offene Eingänge

| Bausteintyp | offener Eingang zählt als |
|---|---|
| UND | **1** |
| ODER | **0** |

Bei UND-Blöcken kann man also unbenutzte Eingänge einfach offen lassen. Bei ODER-Blöcken
ist ein vergessener Eingang dagegen wirkungslos und fällt nicht auf.

### Eingänge invertieren statt NICHT-Blöcke setzen

Rechtsklick auf einen Eingang eines Gatters. Spart Bausteine und macht den Plan lesbarer.

### Lange Leitungen auftrennen

Rechtsklick auf eine Verbindung → **Verbindung auftrennen**. Die Leitung verschwindet
optisch und wird durch Referenzmarken ersetzt. Bei Signalen wie M1 der Standardweg,
sonst wird der Plan unlesbar.

Nachteil: Aufgetrennte Verbindungen findet man nur noch über den Rechtsklick auf den
Baustein oder über die Blockübersicht.

### Blocknamen vergeben

Die automatischen Nummern B001, B002 verschieben sich beim Einfügen neuer Bausteine.
Die Namen bleiben. Und im Parametriermenü des TDE erscheinen genau diese Namen — sie
sind die Beschriftung der Bedienoberfläche.

---

## Bausteine im Detail

### Zähler: Aus-Schwelle auf 1, nicht auf 0

Die Regel lautet: Ist die Ein-Schwelle größer oder gleich der Aus-Schwelle, wird der
Ausgang 1 bei Zählwert ≥ Ein und 0 bei Zählwert **kleiner als** Aus.

Bei Aus = 0 ist die Bedingung „Zählwert kleiner als 0" nach dem Reset nicht erfüllt —
der Ausgang kann gesetzt bleiben. Mit Aus = 1 fällt er nach dem Reset zuverlässig ab.

### Schnelle Zähleingänge nur bei direkter Verbindung

Die Eingänge I3 bis I6 der 24CE zählen bis 5 kHz, aber nur, wenn sie **unmittelbar** am
Cnt-Eingang eines Zählers hängen. Sitzt ein Gatter dazwischen, gilt die normale
Zykluszeit.

### Rückkopplung über einen Merker führen

Den Ausgang eines Zählers direkt auf seinen eigenen Reset zu legen bemängelt Soft
Comfort als geschlossene Schleife. Über einen Merker funktioniert es sauber: Der Ausgang
schreibt den Merker, der Merker wird im nächsten Zyklus gelesen und setzt zurück.

Nebeneffekt: Der so erzeugte Impuls ist genau einen Zyklus lang — ideal als
Umdrehungsimpuls. Ein Zählimpuls, der genau in diesen Zyklus fällt, geht allerdings
verloren.

### Selbsthalterelais: Reset hat Vorrang

Liegen S und R gleichzeitig an, gewinnt R. Darauf lässt sich Sicherheit aufbauen:
Solange eine Abbruchbedingung ansteht, kann der Zyklus nicht gesetzt werden.

### Analog-Arithmetik als Konstantengeber

Die LOGO hat keinen einfachen Baustein für eine einstellbare Analogkonstante. Der
Umweg: Analog-Arithmetik mit En auf hi, V1 als Wert, V2 bis V4 auf 0 und alle Operatoren
auf Plus. V1 ist dann der am Display einstellbare Sollwert.

### PWM schafft nur 10 Hz

Die minimale Periodendauer des Impulsbreitenmodulators beträgt 0,1 s. Für Heizungen
und ähnlich träge Lasten reicht das. Bei Magnetspulen führt es zu Brummen und
schwankender Kraft, weil die Spule zwischen den Impulsen entregt.

Abhilfe: Freilaufdiode parallel zur Spule. Sie hält den Strom in den Pausen aufrecht.
Für wirklich glatte Ströme ist ein externes PWM-Modul im Kilohertzbereich die bessere
Wahl.

**Wichtig:** Ohne eingestellte Periodendauer gibt der Baustein gar nichts aus. Ein Wert
von 00:00 s ist ein stiller Fehler.

### Zeitbasis prüfen

`00:20s` bedeutet 0,20 Sekunden. `04:50s` bedeutet 4,50 Sekunden. `02:00m` bedeutet zwei
Minuten. Wer die Basis übersieht, stellt aus Versehen das Zweihundertfache ein — bei
einer Fahrzeit fällt das sofort auf, bei einer Überwachungszeit erst nach Wochen.

---

## Meldetexte und TDE

### Prioritäten müssen eindeutig sein

**Jede Priorität darf nur einmal vergeben werden.** Bei mehreren aktiven Texten gewinnt
der mit der höchsten Zahl. Wer zwei Texte auf dieselbe Priorität legt, bekommt einen
davon nie zu sehen.

Sinnvolle Staffelung: Dauermasken mit En auf hi bekommen die niedrigsten Werte, danach
Betriebsanzeigen, dann Hinweise, dann Störungen, ganz oben Startbild und Sonderzustände.

### Jeder Meldetext braucht einen Ausgang

Der Baustein hat einen Q-Ausgang, der 1 ist, solange der Text angezeigt wird. Für die
Anzeige braucht man ihn nicht, aber Soft Comfort verlangt, dass er irgendwo endet.
Der saubere Weg ist eine **offene Klemme** aus der Blockauswahl.

### Meldeziel nicht vergessen

Im Eigenschaftsdialog wird eingestellt, ob der Text auf dem LOGO-Display, auf dem TDE
oder auf beiden erscheint. Wird das übersehen, landet die Maske nur im Schaltschrank.

### Die Standardanzeige lässt sich nicht bearbeiten

Datum und Uhrzeit gehören zur Firmware. Verdrängen kann man sie durch einen eigenen
Meldetext mit En auf hi und niedriger Priorität. Dann ist die Standardanzeige praktisch
nie zu sehen.

Ein blinkendes Datum bedeutet übrigens nur, dass die Uhr nicht gestellt ist.

### Werte am TDE ändern

Die Tastenfolge ist nicht selbsterklärend:

1. **ESC mindestens eine Sekunde halten** — kurzer Druck reicht nicht
2. Änderbare Werte werden mit einem schwarzen Balken hinterlegt
3. Mit den Pfeiltasten zum Wert navigieren
4. **Enter** drücken, um in den Schreibmodus zu gelangen
5. Pfeiltasten ändern, Enter bestätigt, ESC verlässt

Voraussetzungen: Weder der **Baustein** noch der **Meldetext** dürfen geschützt sein.
Der Schutz am Meldetext wird oft übersehen.

### Nur Sollwerte sind editierbar

| Parameter | editierbar |
|---|---|
| On, Off | ja |
| T (eingestellte Zeit) | ja |
| V1 bis V4 | ja |
| Cnt (Zählstand) | **nein** |
| Ta (abgelaufene Zeit) | **nein** |

Cnt ist ein Laufzeitwert, den die LOGO selbst schreibt. Wer ihn in eine Parametermaske
einbaut, sieht dort im Stillstand nur Nullen und kann nichts ändern.

### Funktionstasten als Programmeingänge

F1 bis F4 des TDE liegen in der Blockauswahl unter **Konstanten und
Verbindungsklemmen → LOGO! TDE Funktionstasten** und lassen sich wie normale Eingänge
verwenden. Das ersetzt Verdrahtungsaufwand am Bedientableau.

Die Cursortasten sind ebenfalls verfügbar, funktionieren aber nur in bestimmten
Anzeigezuständen. Für Bedienfunktionen sind die F-Tasten die verlässliche Wahl.

Eine Taste kann doppelt genutzt werden, wenn die beiden Funktionen sich ausschließen —
in diesem Projekt ist F2 im Stillstand Handbetrieb und während des Zyklus die
Höhenmarkierung.

---

## Übertragen und Sichern

**Ein Programm-Download überschreibt alle am Gerät eingestellten Parameter** mit den im
Projekt hinterlegten. Eingefahrene Werte vorher notieren und ins Projekt eintragen.

Vor jeder Änderung das Programm aus der LOGO hochladen und archivieren.

**Der Anlaufmerker M8 wird nur beim Wechsel nach RUN gesetzt.** Ein Download, nach dem
die LOGO im STOP bleibt, löst ihn nicht aus. Zum Testen von Startbild oder Referenzfahrt
die Spannung aus- und wieder einschalten.
