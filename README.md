# Werkstatt-Dashboard

Wandbildschirm für die Werkstatt, vorgesehen im **Hochformat**:
Tagesplan oben, Aufgaben in der Mitte, Infoboard unten.

Eigenständige App — die Reading-App bleibt unverändert. Läuft auf dem
Samsung QM55B-T über dessen eingebauten URL Launcher, ohne zusätzlichen PC.
Das Gerät ist laut Handbuch für Hochformat freigegeben (max. 15° Neigung).

```
https://elektrains.github.io/tagesplanung-dashboard/
```

## Aufteilung

Oben der Tagesplan als gewohnte Matrix mit den X — **höchstens 48 % der Höhe**,
damit darunter genug Platz bleibt. Die Aufgaben nehmen so viel Raum wie sie
Inhalt haben, der ganze Rest fällt ans Infoboard.

Passt die Matrix nicht in ihren Deckel — viele Baustellen, viele Mitarbeiter —,
wird **nur sie** heruntergerechnet, nicht die ganze Seite. Bei 16 Baustellen
landet sie bei Faktor 0,87 und bleibt vollständig sichtbar, während Aufgaben
und Infoboard unberührt bleiben.

Ein Hinweis zur Lesbarkeit: im Hochformat ist der Schirm 68,5 cm breit, die
18 Spalten lassen also rund 3,8 cm pro Mitarbeiter. Die Baustellennamen werden
14 mm hoch (bequem auf gut zwei Meter), die senkrechten Mitarbeiternamen
12 mm. Das ist ein Bildschirm zum Hingehen, nicht zum Lesen quer durch die
Werkstatt.

## Was angezeigt wird

**Tagesplan** — alle aktiven Mitarbeiter mit ihren Einsätzen. Dieselben Daten
wie in der Tagesplanung, hier nur lesbar.

**Aufgaben** — Werkstattarbeiten mit Termin und Zuweisung. Überfällige rot.
Blendet sich aus, wenn keine erfasst sind.

**Infoboard** — Mitteilungen mit Titel, Text und optionalem Datum. Eine
Mitteilung kann eine Anmeldeliste haben; dann tippt jeder am Bildschirm seinen
Namen an. Mitteilungen mit "Anzeigen bis" verschwinden nach dem Datum von
selbst.

**Eigene Fragen je Mitteilung.** Zu einer Anmeldeliste lassen sich Fragen
stellen — "Partner kommt mit" als Ja/Nein, "Welche Tage" als Auswahl
Fr–So / Fr+Sa / nur Sa, "Halbtax", "Skiabo nötig". Wer sich einträgt,
beantwortet sie mit grossen Knöpfen. Auf dem Board steht darunter die Summe:
*Partner kommt mit: Ja 7 · Nein 5* — das, was man zum Reservieren braucht.

Jede Frage behält intern ihre Kennung, auch wenn der Text später geändert
wird. Sonst verlören bereits gegebene Antworten ihren Bezug.

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
3. **Auf dem Display** — unter *System → Abspielen mit* auf **Benutzerdefinierte
   App** stellen, dann *Home → App-Verwaltung → Web-App installieren* und die
   Adresse oben eintragen (ohne `?scale=`, die Voreinstellung ist 1.6).

   Der Samsung-Installer liest dabei `sssp_config.xml` aus dem Wurzelverzeichnis
   — eine Datei mit Version, Grösse und Name, mehr nicht. Ohne sie bricht die
   Installation mit „Custom app not installed" ab, auch wenn die Seite im
   Browser einwandfrei läuft. Holt der Bildschirm nach einem Deploy die neue
   Fassung nicht, `<ver>` darin hochzählen und ihn neu starten.

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
