# Palettenwickler Retrofit — Siemens LOGO! 8

Vollständige Dokumentation für den Ersatz einer defekten Originalsteuerung an einem
Drehteller-Palettenwickler durch eine Siemens LOGO! 8.

**Referenzmaschine:** H. Böhm Typ 2020, Baujahr 2002, Frequenzumrichter Hitachi L100

---

## Was dieses Repository leistet

Es ist für drei Leserkreise geschrieben:

**Du bedienst die Maschine.** → [Bedienung](docs/05-parameter-und-bedienung.md)

**Du sollst die Maschine warten oder reparieren.** → [Hardware](docs/01-hardware.md),
[Elektrik und Signale](docs/02-elektrik-und-signale.md),
[Störungssuche](docs/06-inbetriebnahme-und-wartung.md)

**Du willst deine eigene Maschine auf LOGO! umbauen.** → [Nachbau](docs/08-nachbau.md)
und [LOGO-Praxiswissen](docs/07-logo-praxiswissen.md)

---

## Dokumente

| Datei | Inhalt |
|---|---|
| [01-hardware.md](docs/01-hardware.md) | Steuerung, Sensoren, Aktoren, Bauteilliste mit Ersatzteil-Spezifikationen |
| [02-elektrik-und-signale.md](docs/02-elektrik-und-signale.md) | Vollständige E/A-Belegung, Sicherheitskette, Schützverriegelung |
| [03-frequenzumrichter.md](docs/03-frequenzumrichter.md) | Hitachi L100: alle Parameter, Diagnose, Nachlaufberechnung |
| [04-programm.md](docs/04-programm.md) | Aufbau des Schaltprogramms und vollständige Bausteinliste |
| [05-parameter-und-bedienung.md](docs/05-parameter-und-bedienung.md) | Bedienung am TDE, alle einstellbaren Werte, Meldetexte |
| [06-inbetriebnahme-und-wartung.md](docs/06-inbetriebnahme-und-wartung.md) | Gestufte Inbetriebnahme, Simulation, Störungssuche |
| [07-logo-praxiswissen.md](docs/07-logo-praxiswissen.md) | Fallstricke der LOGO! 8, die in keinem Handbuch stehen |
| [08-nachbau.md](docs/08-nachbau.md) | Übertragung auf eine andere Maschine |

---

## Funktionsumfang

- Halbautomatischer Wickelzyklus mit Bodenwicklungen, Aufwärtsfahrt, Kopfwicklungen,
  Abwärtsfahrt und Abschlusswicklungen
- **Winkelgenaue Positionierung** der Palette über Zahnradsensor und Schleichfahrt
- **Drei Betriebsarten** über den Drehschalter: Vollzyklus, nur aufwärts, nur Drehteller
- **Stufenlose Folienspannung** über PWM-Ansteuerung der Bremse
- Automatische **Referenzfahrt** beim Einschalten
- Pause-Funktion, Handbetrieb für den Wickelwagen
- Fahrzeit- und Drehtellerüberwachung mit Klartext-Störmeldungen
- Bedienung und Parametrierung über LOGO! TDE

---

## Sicherheitshinweis

Diese Dokumentation beschreibt einen Steuerungsretrofit an einer Maschine mit
gefährlichen Bewegungen. Die sicherheitsgerichteten Funktionen der Referenzmaschine —
Not-Halt, Türüberwachung, Bodenkontakt, Motorschutz — sind **hardwareseitig** aufgebaut
und schalten die Aktoren unabhängig von der Steuerung ab.

Eine LOGO! ist **keine sicherheitsgerichtete Steuerung**. Sie darf keine
Sicherheitsfunktion allein übernehmen. Wer diesen Umbau nachvollzieht, muss die
Sicherheitstechnik seiner Maschine eigenständig bewerten und verantworten.

Bekannte Schwachstelle der Referenzmaschine: Die obere Endlage ist **nicht**
hardwareseitig abgesichert, sondern nur über einen Reedkontakt und eine
Fahrzeitüberwachung im Programm. Siehe [Offene Punkte](docs/06-inbetriebnahme-und-wartung.md#offene-punkte).

---

## Software

- **LOGO!Soft Comfort** ab V8.2, läuft unter Windows, Linux und macOS
- Zielgerät im Projekt: **LOGO! 8.FS4**
- Programmiersprache: FUP (Funktionsplan)

---

## Lizenz und Haftung

Die Dokumentation wird ohne Gewähr bereitgestellt. Prüfe alle Angaben gegen das
Handbuch deiner Geräte und deine eigene Anlage, bevor du sie anwendest.
