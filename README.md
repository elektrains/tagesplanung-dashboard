# Werkstatt-Dashboard

Wandbildschirm für die Werkstatt, vorgesehen im **Hochformat**:
Tagesplan oben, Aufgaben in der Mitte, Infoboard unten.

Eigenständige App — die Reading-App bleibt unverändert. Läuft auf dem
Samsung QM55B-T über dessen eingebauten URL Launcher, ohne zusätzlichen PC.
Das Gerät ist laut Handbuch für Hochformat freigegeben (max. 15° Neigung).

```
https://elektrains.github.io/tagesplanung-dashboard/
```

## Warum nicht die Matrix aus der Management-App

Im Hochformat ist der Bildschirm 68,5 cm breit. Die Planmatrix hat bei
16 Mitarbeitern 18 Spalten — rechnerisch 3,8 cm pro Spalte. Das liest aus zwei
Metern niemand mehr.

Deshalb steht hier **eine Zeile pro Mitarbeiter**: Name, Baustelle, Fahrzeug.
Mehrere Einsätze am selben Tag stehen untereinander, Halbtage mit VM/NM
gekennzeichnet. Abwesend und Schule färben die Zeile.

Gemessen am echten Seitenverhältnis wird der Mitarbeitername so 16,2 mm hoch
statt 9,6 mm in der Matrix — bequem lesbar auf etwa 2,7 statt 1,6 Meter.

## Was angezeigt wird

**Tagesplan** — alle aktiven Mitarbeiter mit ihren Einsätzen. Dieselben Daten
wie in der Tagesplanung, hier nur lesbar.

**Aufgaben** — Werkstattarbeiten mit Termin und Zuweisung. Überfällige rot.
Blendet sich aus, wenn keine erfasst sind.

**Infoboard** — Mitteilungen mit Titel, Text und optionalem Datum. Eine
Mitteilung kann eine Anmeldeliste haben; dann tippt jeder am Bildschirm seinen
Namen an. Mitteilungen mit "Anzeigen bis" verschwinden nach dem Datum von
selbst.

## Was bedient werden kann

Drei Knöpfe: `‹`, `Heute`, `›`. Der Plan selbst lässt sich nicht ändern — es
gibt keine Funktion, die ihn anfassen könnte.

Nach **30 Sekunden ohne Berührung** springt die Anzeige auf heute zurück, mit
sichtbarem Countdown. Um Mitternacht wechselt sie ebenfalls selbst.

**Abhaken und Anmelden sind umkehrbar**: nochmal antippen nimmt es zurück.
Erledigte Aufgaben bleiben durchgestrichen stehen, bis sie in der
Management-App entfernt werden.

## Selbstanpassung an den Tag

Ein Wandbildschirm wird nicht gescrollt. Passt ein Tag nicht auf den Schirm —
viele Mitarbeiter, lange Aufgabenliste, mehrere Mitteilungen —, verkleinert
sich die ganze Darstellung so weit, bis alles sichtbar ist, und geht an ruhigen
Tagen wieder hoch. Lieber etwas kleiner als unten abgeschnitten: eine fehlende
Zeile fällt an der Wand niemandem auf.

Im Querformat rutschen die Bereiche enger zusammen, damit es auch so benutzbar
bleibt — gedacht ist die App aber fürs Hochformat.

## Erfassen

In der Management-App im Reiter **Werkstatt**: oben die Aufgaben, darunter das
Infoboard. Beim Erfassen einer Mitteilung lässt sich ankreuzen, ob eine
Anmeldeliste dazugehört. Die Namen der Angemeldeten stehen dort zum Nachlesen.

## Einrichtung

1. **Anonyme Anmeldung** — Firebase-Konsole → Authentication → Anmeldemethode →
   Anonym. Ohne das zeigt der Bildschirm „Nur Ansicht"; der Plan erscheint
   trotzdem. Automatische Bereinigung ausgeschaltet lassen.
2. **Datenbankregeln** — siehe `firebase-regeln.txt`.
3. **Auf dem Display** — URL Launcher öffnen, Adresse eintragen. Je nach
   Firmware *App Management → Install Custom App* (Tizen 6.5+) oder
   *URL Launcher → Install Web App*. Steht er nicht zur Auswahl, zuerst unter
   *System → Play via* umschalten.

## Schriftgrösse

Voreinstellung 1.6. Zum Ändern die Adresse einmal mit `?scale=2.4` aufrufen —
der Wert bleibt im Gerät gespeichert, auch nach einem Neustart. Bereich 0.6
bis 5.0.

Rechnet der Tizen-Browser die Seite mit 2160 statt 1080 Pixeln Breite, brauchen
Sie etwa den doppelten Wert. Physisch kommt dasselbe heraus — einfach so
einstellen, dass es den Schirm füllt.

## Bei Verbindungsproblemen

Bricht die Verbindung länger als zwei Minuten weg, lädt die Seite neu. Verliert
der Bildschirm seine anonyme Anmeldung, meldet er sich von selbst wieder an.

Empfehlung trotzdem: Netzwerkkabel statt WLAN, wenn eines hinkommt.

## Daten

Gelesen wird aus `plans` und `settings`, geschrieben ausschliesslich nach
`werkstattAufgaben` und `infoboard/*/teilnehmer`.

```json
werkstattAufgaben/<id> = {
  "titel": "Lager aufräumen",
  "beschreibung": "Regal 3 bis 5",
  "termin": "2026-11-14",
  "zugewiesen": ["Lukas", "Naim"],
  "erledigt": false,
  "erledigtAm": null,
  "createdAt": 1763040000000
}

infoboard/<id> = {
  "titel": "Weihnachtsessen",
  "text": "Restaurant Traube, 19:00 Uhr.",
  "datum": "2026-12-19",
  "bis": "2026-12-20",
  "anmeldung": true,
  "teilnehmer": { "Timo": 1763040000000 },
  "createdAt": 1763040000000
}
```

Namen als Schlüssel werden entschärft: `. $ # [ ] /` sind in Firebase-Pfaden
nicht erlaubt und werden durch `_` ersetzt.
