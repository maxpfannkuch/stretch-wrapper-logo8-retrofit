# 01 — Hardware

## Anlagenübersicht

| Merkmal | Angabe |
|---|---|
| Hersteller | H. Böhm |
| Typ | 2020 |
| Baujahr | 2002 |
| Bauart | Drehteller-Palettenwickler, halbautomatisch |
| Antrieb Drehteller | Drehstrommotor über Frequenzumrichter |
| Antrieb Wickelwagen | Drehstrommotor, Wendeschützschaltung, feste Drehzahl |
| Folienbremse | elektromagnetische Reibbremse 24 V DC |

Am Wickelwagen findet sich ein Bauteil-Etikett von EPI-PACK d.o.o. (Slowenien),
Bezeichnung „Pokrov vozička PROTO", datiert 2003/2004 — vermutlich eine spätere
Ersatzteillieferung.

---

## Steuerung

| Gerät | Bestellnummer | Beschreibung |
|---|---|---|
| Grundgerät | 6ED1052-1CC08-0BA0 | LOGO! 24CE, Generation **8.2** |
| Erweiterung | 6ED1055-1NB10-0BA2 | DM16 24R |
| Display | 6ED1055-4MH08-… | LOGO! TDE |

### LOGO! 24CE

- Versorgung 24 V DC
- 8 Digitaleingänge, davon I1/I2 alternativ als AI3/AI4 und I7/I8 als AI1/AI2 (0–10 V)
- **Schnelle Zähleingänge: I3, I4, I5, I6** (bis 5 kHz)
- 4 Transistorausgänge, **max. 0,3 A** je Ausgang
- Ethernet, Display, Tasten

### DM16 24R

- 8 Digitaleingänge 24 V DC
- 8 Relaisausgänge 5 A, potentialfreie Kontakte

In der Referenzanlage sind die Relaisausgänge mit 24 V durchgebrückt, um die
Schützspulen direkt zu schalten. Wer einen potentialfreien Kontakt braucht — etwa für
den Reset-Eingang des Umrichters — muss die Brücke an der betreffenden Stelle
auftrennen.

### Erweiterbarkeit

LOGO-Module werden **ausschließlich seitlich über den Busstecker angereiht**. Ein
Verlängerungskabel in eine zweite Hutschienenreihe existiert nicht. Reicht der Platz
in der Reihe nicht, ist eine Erweiterung nicht möglich.

Das war der Grund, warum die Folienbremse hier über ein externes PWM-Modul statt über
ein Analogausgangsmodul geregelt wird.

### Netzwerk

| Gerät | IP-Adresse |
|---|---|
| LOGO! Basismodul | 192.168.0.5 |
| LOGO! TDE | 192.168.0.2 |
| Programmier-PC | 192.168.0.10 |

Subnetzmaske überall 255.255.255.0. Am TDE muss unter „IP-Adresse des Basismoduls"
die 192.168.0.5 eingetragen sein. Beide Geräte gehören an denselben Switch.

---

## Frequenzumrichter

**Hitachi L100.** Details siehe [03-frequenzumrichter.md](03-frequenzumrichter.md).

Kurzfassung: 5 programmierbare Digitaleingänge (Klemmen 1–5, gemeinsam P24),
Analogsollwerteingänge H/O/OI/L, zwei programmierbare Digitalausgänge (Klemmen 11 und
12, gemeinsam CM2), Störmelderelais.

---

## Schaltgeräte

| Bezeichnung | Typ | Funktion |
|---|---|---|
| KM1 | Telemecanique LP1K06, Spule 24 V DC | Wickelwagen **abwärts** |
| KM2 | Telemecanique LP1K06, Spule 24 V DC | Wickelwagen **aufwärts** |
| KM0 | mit Hilfsblock LA1KN11 | Teil der Sicherheitstechnik |
| QF2 | Telemecanique GV2ME05, eingestellt 0,63–1 A | Motorschutz Hubmotor |
| QF1 | Schrack D10, 3-polig | Leitungsschutz Umrichter |
| QF3 | Schrack C2 | Leitungsschutz Steuerung |
| FILT | AF-1-009-0-0 | Netzfilter |

Aus der Einstellung des GV2ME05 lässt sich die Leistung des Hubmotors auf etwa
0,25 kW schätzen.

---

## Sensorik

### Höhenerkennung

**SICK WT27-2P410**, Artikelnummer 1015138

| Merkmal | Wert |
|---|---|
| Bauart | Reflexions-Lichttaster, energetisch, ohne Reflektor |
| Versorgung | 10–30 V DC |
| Ausgang | PNP, antivalent (Q und Q invertiert) |
| Tastweite | bis 1500 mm |
| Anschluss | M12, 4-polig |

Anschlussbelegung:

| Pin | Ader | Funktion |
|---|---|---|
| 1 | braun | L+ |
| 2 | weiß | Q invertiert |
| 3 | blau | M |
| 4 | schwarz | Q — **hier verwendet** |

**Zeitstufe am Sensor:** Der untere Drehschalter wählt die Funktion, die beiden oberen
Potentiometer die Dauer. t1/t3 = Einschaltverzögerung, t2/t4 = Ausschaltverzögerung.
Rechte Spalte = Zeitbereich unter 0,5 s, linke Spalte = über 0,5 s, t0 = keine
Verzögerung.

**Eingestellt ist t0.** Die Filterung erfolgt im Programm über B007 LS_Filter, weil der
Wert dort am Display einstellbar ist und nicht mit dem Schraubendreher justiert werden
muss.

**Empfindlichkeit einstellen:** Mehrgang-Potentiometer am Sensor, mehrfach drehen. Erst
bei freier Sicht zurückdrehen, bis der Ausgang abfällt, dann mit Testobjekt im
typischen Abstand langsam aufdrehen, bis das Signal sicher kommt.

**Ersatzteil-Spezifikation** — Hersteller beliebig, Anschluss muss passen:

- M12, 4-polig, A-codiert
- 10–30 V DC, PNP, Schließer, hellschaltend
- Reflexions-Lichttaster **mit Hintergrundausblendung**
- Tastweite mindestens 1000 mm einstellbar

Hintergrundausblendung ist dem energetischen Prinzip vorzuziehen: Der energetische
Taster wertet die zurückkommende Lichtmenge aus und erkennt dunkle oder glänzende Ware
schlechter. Ein Taster mit Hintergrundausblendung wertet den Winkel aus und ist
weitgehend farbunabhängig.

Passende Familien: Pepperl+Fuchs ML100-8-H, ifm O5H, Baumer FHDH, SICK W27-3.

### Bodenkontakt

**Bernstein I88-U1Z W**, Artikelnummer 608.6103.008

| Merkmal | Wert |
|---|---|
| Bauart | Positionsschalter mit Rollenhebel |
| Kontakt | 1 Wechsler |
| Belastbarkeit | 10 A / 500 V AC |
| Schutzart | IP65 |

Verwendet wird der **Öffner**, in Reihe in der Sicherheitskette. Löst aus, sobald der
Wickelkopf den Boden berührt.

Hinweis: Der Kontakt ist für AC-Lasten ausgelegt. Am 24-V-Steuerkreis fließen nur
wenige Milliampere, was über die Jahre zu Kontaktoxidation führen kann. Bei sporadischen
Aussetzern zuerst den Kontakt reinigen.

### Drehtellersensor

Magnetsensor auf ein Zahnrad am Drehteller.

| Merkmal | Wert |
|---|---|
| Zähne pro Umdrehung | **64** |
| Auflösung | 5,625 Grad pro Zahn |
| Impulsfrequenz bei 12 min⁻¹ | 12,8 Hz |
| Impulsfrequenz bei 3 min⁻¹ | 3,2 Hz |

Die Frequenz liegt weit unter der Grenze der schnellen Eingänge von 5 kHz.

### Endlagenschalter

Magnetkontakte oben und unten, beide als **Schließer** ausgeführt: Signal nur bei
Magnetkontakt.

**Bekannte Schwäche:** Ein Kabelbruch sieht für die Steuerung genauso aus wie „Endlage
noch nicht erreicht". Unten wird das durch den Bernstein-Bodenkontakt in der
Sicherheitskette abgefangen, oben nur durch die Fahrzeitüberwachung im Programm.

**Justage des unteren Endschalters:** Der Magnetschalter hat ein begrenztes
Schaltfenster. Der Wagen muss nach dem Anhalten innerhalb dieses Fensters stehenbleiben,
sonst meldet I3 keine Endlage und der nächste Start ist gesperrt. Der Abstand zwischen
Ansprechen von I3 und Auslösen des Bodenkontakts muss größer sein als der Nachlaufweg
des Wagens.

---

## Folienbremse

**Warner Electric B6650-631-000-39**, Induktor 24 V DC

Elektromagnetische Reibbremse aus dem Tension-Control-Programm. Das Bremsmoment ist
annähernd proportional zum Spulenstrom, die Bremse ist also **konstruktiv für stufenlose
Regelung vorgesehen**.

Angeschlossen an Q2, einen Transistorausgang mit 0,3 A. Der Spulenstrom liegt damit
unter diesem Wert.

**Ansteuerung:** PWM über den Baustein B066 mit 0,1 s Periodendauer, Sollwert aus
B065 Bremskraft in Prozent.

**Verbesserungsmöglichkeit:** Eine Freilaufdiode parallel zur Spule (Kathode an Plus,
zum Beispiel 1N4007) glättet den Spulenstrom zwischen den Impulsen und macht die
Bremskraft gleichmäßiger. Bei 10 Hz PWM-Frequenz ist das spürbar.

---

## Bedientableau

| Element | Funktion |
|---|---|
| Grüner Leuchttaster | Start |
| Roter Leuchttaster | Stop |
| Weißer Leuchttaster | Reset der Sicherheitskette |
| Drehschalter 3-stufig | Betriebsartenwahl |
| Not-Halt | nimmt Umrichter und beide Schütze stromlos |
| Potentiometer | Drehzahl Drehteller, direkt am Umrichter |

Die Piktogramme am Drehschalter: Stellung 1 zeigt einen Stapel mit Pfeil auf und ab,
Stellung 2 einen Stapel mit Pfeil aufwärts, Stellung 3 einen Stapel ohne Pfeil.

Drei weitere Potentiometer aus der Originalsteuerung (Wicklungen unten, Wicklungen
oben, Folienspannung) sind **stillgelegt** — diese Werte werden jetzt am TDE
eingestellt. Ihre Leitungen liegen auf I1, I2 und I7, also auf den Klemmen, die als
Analogeingänge AI3, AI4 und AI1 nutzbar wären.
