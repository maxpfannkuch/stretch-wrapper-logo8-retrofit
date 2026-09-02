# 05 — Bedienung und Parameter

---

## Für den Maschinenbediener

### Normaler Ablauf

1. Drehschalter auf die gewünschte Betriebsart stellen
2. Palette auf den Drehteller stellen
3. Folie an der Palette befestigen
4. **Grünen Taster** drücken
5. Maschine wickelt und stoppt in der Ausgangsposition
6. Folie schneiden

### Bedienelemente

| Element | Funktion |
|---|---|
| Grüner Taster | Start |
| Roter Taster | Stop, gleichzeitig Quittieren einer Störung |
| Weißer Taster | Reset nach Türöffnung oder Bodenkontakt |
| Drehschalter | Betriebsart |
| Potentiometer | Drehzahl Drehteller |
| Not-Halt | Sofortabschaltung |

### Betriebsarten am Drehschalter

| Stellung | Symbol | Ablauf |
|---|---|---|
| 1 | Stapel mit Pfeil auf und ab | Vollzyklus |
| 2 (Mitte) | Stapel mit Pfeil auf | wickelt hoch, endet oben |
| 3 | Stapel ohne Pfeil | nur Drehteller, Wagen steht |

Die Stellung vor dem Start wählen. Ein Verstellen während des Zyklus wirkt erst ab dem
nächsten Übergang.

Nach Betriebsart 2 steht der Wagen oben. Der nächste Start ist gesperrt — mit **F3**
herunterfahren.

### Tasten am Display

| Taste | Funktion |
|---|---|
| F1 | Pause, nochmal drücken setzt fort |
| F2 | Wagen aufwärts (nur im Stillstand) — während des Zyklus: Höhe erreicht |
| F3 | Wagen abwärts (nur im Stillstand) |
| F4 | Störung quittieren |

### Wenn die weiße Lampe aus ist

Tür am Wickelkopf geschlossen? Wickelkopf vom Boden frei? Dann weißen Taster drücken.

Bleibt die Lampe an, aber die Maschine startet nicht, hat der Motorschutzschalter
im Schaltschrank ausgelöst.

---

## Parameter am TDE ändern

### Die richtige Tastenfolge

Das ist der Punkt, an dem fast alle hängenbleiben:

1. Mit **Auf und Ab** zur gewünschten Maske blättern
2. **ESC mindestens eine Sekunde gedrückt halten**, dann loslassen
3. Die änderbaren Werte werden jetzt **mit einem schwarzen Balken** hinterlegt
4. Mit den **Pfeiltasten** zum gewünschten Wert navigieren
5. **Enter** drücken — jetzt bist du im Schreibmodus für diesen Wert
6. Mit **Auf und Ab** den Wert ändern, mit **Links und Rechts** die Stelle wechseln
7. **Enter** bestätigt
8. **ESC** verlässt den Schreibmodus

Ein kurzer Druck auf ESC bringt nur einen blinkenden Cursor, aber keinen Schreibmodus.

### Wenn kein schwarzer Balken erscheint

Drei mögliche Ursachen:

- **Parameterschutz am Baustein aktiv.** Im Eigenschaftsdialog abschalten.
- **Parameterschutz am Meldetext aktiv.** Der Meldetextbaustein hat eine eigene
  Schutzeinstellung. Wird oft übersehen.
- **Der Wert ist gar nicht editierbar.** `Cnt` ist der laufende Zählstand, den die
  LOGO selbst schreibt — der bekommt nie einen Balken. Editierbar sind nur Sollwerte
  wie `On`, `T` und `V1`.

### Alternative: Parametriermenü

Über **ESC** ins Gerätemenü, dann zum Punkt Parameter. Dort steht jeder Baustein mit
seinem Namen und Wert. Funktioniert unabhängig von den Meldetexten — deshalb sind gute
Blocknamen wichtig, sie sind die Beschriftung dieser Bedienoberfläche.

---

## Vollständige Parameterliste

### Alltagseinstellungen — Maske „Einstellungen"

| Baustein | Parameter | Wert | Bedeutung |
|---|---|---|---|
| B031 Z_Unten | On | 3 | Wicklungen am Palettenfuß |
| B032 Z_Oben | On | 3 | Wicklungen am Palettenkopf |
| B033 Z_abschluss | On | 2 | Wicklungen zum Abschluss |
| B065 Bremskraft | V1 | 55 | Folienspannung in Prozent |

Die Gesamtzahl der Bodenwicklungen ist `Z_abschluss` plus eins — die
Positionierumdrehung kommt noch dazu.

### Einrichtwerte — Maske „Positionierung"

| Baustein | Parameter | Wert | Bedeutung |
|---|---|---|---|
| B035 Z_schleich | On | 32 | Zahn, ab dem auf 15 Hz umgeschaltet wird |
| B036 Z_stop | On | 64 | Zahn, bei dem der Teller abgeschaltet wird |
| B006 Zahnzaeler | On | 64 | Zähne pro Umdrehung |
| B067 Ueberfahrt | T | 4,50 s | Nachlauf über die Palettenoberkante |

### Nicht am Display, nur in Soft Comfort

| Baustein | Parameter | Wert | Bedeutung |
|---|---|---|---|
| B007 LS_Filter | T | 0,50 s | Filterzeit Lichtschranke |
| B027 MinFahrzeit | T | 3,00 s | Mindestdauer Aufwärtsfahrt |
| B038 ZeitAuf | T | 90 s | Überwachung Aufwärtsfahrt |
| B039 ZeitAb | T | 90 s | Überwachung Abwärtsfahrt |
| B040 Anlaufzeit | T | 45 s | Karenzzeit Drehtellerüberwachung |
| B041 ImpulsWaeche | T | 40 s | max. Zeit ohne Umdrehungsimpuls |
| B069 RefTimeout | T | 90 s | Überwachung Referenzfahrt |
| B062 FertigHalten | T | 15 s | Anzeigedauer „Zyklus beendet" |
| B077 WilkommenHal | T | 10 s | Anzeigedauer Startbild |
| B088 Lichtnachlauf | T | 2 min | Nachlauf Displaybeleuchtung |
| B004 Startimpuls | Impulsdauer | 0,20 s | fest, nicht freigeben |
| B056 Blinker | 0,5 s / 0,5 s | | Blinkfrequenz Störung |
| B066 BremsePWM | Periode | 0,10 s | Minimum der LOGO |

### Sinnvolle Grenzen

Wo der Bausteindialog Minimal- und Maximalwerte zulässt, sollten sie gesetzt werden:

| Parameter | Bereich |
|---|---|
| Wicklungen | 1 bis 20 |
| Folienzug | 20 bis 100 |
| Schleichfahrt ab Zahn | 10 bis 50 |
| Stop bei Zahn | 40 bis 70 |

**Warnung beim Stoppzahn:** Ein Wert unter dem Schleichfahrtzahn führt dazu, dass nie
auf Schleichfahrt umgeschaltet wird. Werte über 64 sind zulässig — die Positionierzähler
laufen weiter, solange M9 aktiv ist, der Teller dreht dann etwas mehr als eine Umdrehung.

---

## Meldetexte

Prioritäten müssen **eindeutig** sein — jede Zahl darf nur einmal vergeben werden.
Bei mehreren aktiven Texten gewinnt der mit der höchsten Zahl.

| Prio | Baustein | En | Zweck |
|---|---|---|---|
| 20 | B089 F_Info | hi | Tastenbelegung, ersetzt die Datumsanzeige |
| 21 | B080 MT_Param | hi | Alltagseinstellungen |
| 22 | B081 MT_Position | hi | Einrichtwerte |
| 23 | B061 MT_Betrieb | M1 | Laufender Zyklus mit Zählerständen |
| 24 | B058 MT_Fertig | B062 | Zyklus beendet |
| 25 | B059 MT_NUnten | B063 | Wagen nicht unten |
| 26 | B060 MT_StoerAuf | B038 | Störung Aufwärtsfahrt |
| 27 | B082 MT_StoerAb | B039 | Störung Abwärtsfahrt |
| 28 | B083 MT_StoerTell | B042 | Störung Drehteller |
| 29 | B084 MT_StoerRef | B069 | Störung Referenzfahrt |
| 30 | B079 MT_Referenz | M21 | Referenzfahrt läuft |
| 31 | B078 MT_Wilkommen | B077 | Startbild |

Die Störungstexte haben **Quittierung ein**, alle anderen aus.

### Inhalte

**B089 F_Info**
```
TASTENBELEGUNG
F1 = Pause / Weiter
F2 = Wagen auf
     im Zyklus: Hoehe
F3 = Wagen ab
F4 = Stoerung quitt.
```

**B080 MT_Param**
```
EINSTELLUNGEN
Wickl. unten:  [On]
Wickl. oben:   [On]
Wickl. absch.: [On]
Folienzug:     [V1]
ESC halten = aendern
```

**B081 MT_Position**
```
POSITIONIERUNG
Schleich an:   [On]
Stop bei:      [On]
Zaehne/Umdr:   [On]
Ueberfahrt:    [T]
ESC halten = aendern
```

**B061 MT_Betrieb**
```
WICKELN LAEUFT
Unten  [Cnt] / [On]
Oben   [Cnt] / [On]
Absch. [Cnt] / [On]
Zug       [V1] %
F2 = Hoehe erreicht
```

**B078 MT_Wilkommen**
```
====================
   BESTCHEM GMBH
 Herzlich Willkommen
   Palettenwickler
====================
```

Weitere Texte siehe Programm. Störungstexte nennen jeweils die vermutete Ursache und
den Hinweis auf F4.

### Regeln für Meldetexte

- Größe auf **6 Zeilen mit 20 Zeichen** stellen, das ist das Format des TDE
- **Meldeziel** auf TDE oder Beide, sonst erscheint der Text nur im Schaltschrank
- Jeder Meldetext braucht einen **Ausgang** — offene Klemme anhängen
- Umlaute sind grenzwertig; am TDE der Referenzanlage werden sie korrekt dargestellt,
  bei anderen Zeichensatzeinstellungen nicht. Im Zweifel „ae" schreiben.

### Variable einfügen

1. Meldetextbaustein per Doppelklick öffnen
2. Im Textfeld an die Zielstelle klicken, der Cursor muss dort blinken
3. In der Blockliste links den Baustein wählen — Suchfeld nutzen, die Liste ist lang
4. In der Parameterliste darunter den Parameter markieren
5. Mit der Pfeilschaltfläche übernehmen oder per Maus an die Cursorposition ziehen

| Parameter | Bedeutung |
|---|---|
| Cnt | aktueller Zählstand, nur Anzeige |
| On | Ein-Schwelle, editierbar |
| V1 bis V4 | Werte der Analog-Arithmetik, editierbar |
| T | eingestellte Zeit, editierbar |
| Ta | abgelaufene Zeit, nur Anzeige |

---

## Startbild des TDE

Zwei Wege:

**Extras → Übertragen → Startbild** überträgt ein Bild direkt ins TDE. Es erscheint
beim Einschalten des Displays, unabhängig vom Schaltprogramm.

**Als Meldetext** mit hoher Priorität und einer Ausschaltverzögerung auf M8 als
Trigger. So ist es in der Referenzanlage gelöst (B077 und B078).

Beide Wege lassen sich kombinieren.

---

## Wichtig beim Übertragen

Ein **Programm-Download aus Soft Comfort überschreibt alle am TDE eingestellten Werte**
mit den im Projekt hinterlegten. Vor jeder neuen Programmversion die eingefahrenen
Werte notieren und ins Projekt eintragen.
