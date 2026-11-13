# Werkstatt-Dashboard

Wandbildschirm für die Werkstatt: Tagesplan oben, Aufgabenliste unten.
Eigenständige App — die Reading-App bleibt unverändert.

Läuft auf dem Samsung QM55B-T über dessen eingebauten URL Launcher,
ohne zusätzlichen PC.

## Was angezeigt wird

**Oben** Minikalender und Legende links, daneben der Tagesplan mit Baustelle,
Fahrzeug und den Mitarbeiterspalten samt Statuspunkten — dieselben Daten wie in
der Tagesplanung.

**Unten** die Werkstatt-Aufgaben: Aufgabe, Beschreibung, Termin und die
zugewiesenen Namen. Überfällige sind rot hinterlegt.

## Was bedient werden kann

Nur drei Knöpfe: `‹`, `Heute`, `›`. Der Plan selbst lässt sich hier nicht
ändern — es gibt keine Funktion, die ihn anfassen könnte.

Nach **30 Sekunden ohne Berührung** springt die Anzeige von selbst auf heute
zurück; ein Countdown zeigt das an. Um Mitternacht wechselt sie ebenfalls
automatisch auf den neuen Tag.

**Aufgaben abhaken**: antippen. Nochmal antippen macht es rückgängig — ein
versehentlicher Haken zwingt niemanden an den Laptop. Erledigte bleiben
durchgestrichen stehen, bis sie in der Management-App entfernt werden.

## Aufgaben erfassen

In der Management-App im Reiter **Werkstatt**. Dort anlegen, bearbeiten,
zuweisen (mehrere Namen pro Aufgabe) und endgültig entfernen. Das rote
Abzeichen am Reiter zeigt, wie viele überfällig sind.

Die Liste ist bewusst getrennt von der To-Do-Liste unter dem Tagesplan: dort
stehen Projektaufgaben, hier Hausarbeiten.

## Einrichtung

1. **Anonyme Anmeldung aktivieren** — Firebase-Konsole → Authentication →
   Sign-in method → Anonym. Ohne das zeigt der Bildschirm „Nur Ansicht" und das
   Abhaken ist gesperrt; der Plan erscheint trotzdem.
2. **Datenbankregeln** — siehe `firebase-regeln-vorschlag.txt`.
3. **Auf dem Display** — URL Launcher öffnen und die Adresse der App eintragen.
   Je nach Firmware: *App Management → Install Custom App* (Tizen 6.5+) oder
   *URL Launcher → Install Web App*. Steht der URL Launcher nicht zur Auswahl,
   zuerst unter *System → Play via* umschalten.

## Schriftgrösse einstellen

Alles skaliert über einen Faktor. Voreinstellung `1.6`, passend für einen
55-Zöller aus wenigen Metern Abstand.

Zum Ändern die Adresse einmal mit `?scale=1.9` aufrufen — der Wert bleibt im
Gerät gespeichert, auch nach einem Neustart. Bereich 0.6 bis 4.0.

## Bei Verbindungsproblemen

Bricht die Verbindung länger als zwei Minuten weg, lädt die Seite von selbst
neu. Ein Wandbildschirm, der nach einem WLAN-Aussetzer veraltete Daten zeigt,
fällt sonst niemandem auf.

Empfehlung trotzdem: Netzwerkkabel statt WLAN, wenn eines hinkommt.

## Daten

Gelesen wird aus `plans` und `settings`, geschrieben ausschliesslich nach
`werkstattAufgaben`. Eine Aufgabe sieht so aus:

```json
{
  "titel": "Lager aufräumen",
  "beschreibung": "Regal 3 bis 5, Altmaterial in die Mulde",
  "termin": "2026-11-14",
  "zugewiesen": ["Lukas", "Naim"],
  "erledigt": false,
  "erledigtAm": null,
  "createdAt": 1763040000000
}
```
