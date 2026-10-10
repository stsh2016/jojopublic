# Wetter Stäfa

Web-App fürs iPhone: aktuelles Wetter und eine Empfehlung, was man anziehen soll (heute und morgen).

Orte zur Auswahl (Tippen auf den Ortsnamen oben öffnet das Menü, die Wahl wird gespeichert):
Stäfa, Uetikon am See, Zürich und Hohentengen am Hochrhein.

Für jeden Ort ausser Stäfa zeigt die App die nächsten 2 Verbindungen von Stäfa dorthin und zurück
(Fahrplan von [transport.opendata.ch](https://transport.opendata.ch/), mit Gleis und Verspätung; nach Uetikon und Zürich nur Züge).
«Details heute» und «Die nächsten Tage» sind zugeklappt und öffnen sich per Tippen.

## Wochenplan

Vor den Zugverbindungen zeigt «Diese Woche» die Termine der nächsten 7 Tage, jeweils mit dem Wetter des Tages.
Termine lassen sich von Hand eintragen oder aus dem iPhone-Kalender übernehmen. Eine Web-App darf den Kalender nicht
selbst lesen, deshalb übernimmt das ein Kurzbefehl, der die App mit den Terminen öffnet:

1. **Kalenderereignisse suchen** – Startdatum ist in den nächsten 7 Tagen, sortiert nach Startdatum (optional: nur bestimmte Kalender).
2. **Wiederholen mit jedem** – darin **Text**: `Startdatum|Enddatum|Ist ganztägig|Titel|Ort`
   (Variable «Wiederholungsobjekt» mit der jeweiligen Eigenschaft; Datumsformat «ISO 8601»).
3. **Text kombinieren** (Wiederholungsergebnisse, Trennzeichen «Neue Zeile»), danach **URL codieren**.
4. **Text**: `https://stsh2016.github.io/jojopublic/#termine=` gefolgt von der codierten Variable.
5. **URLs öffnen**.

Die Termine stehen nur im Teil der Adresse nach `#`, der nicht an den Server gesendet wird, und werden danach
nur lokal im Browser des iPhones gespeichert. Jeder Aufruf des Kurzbefehls ersetzt die zuvor übernommenen Kalendertermine;
von Hand eingetragene Termine bleiben erhalten.

- Reine statische Website: HTML, CSS und JavaScript in einer Datei, kein Build-Schritt, keine Abhängigkeiten.
- Wetterdaten von [Open-Meteo](https://open-meteo.com/) (kostenlos, kein API-Schlüssel, u. a. Modelle von MeteoSchweiz).
- Als App installierbar über Safari → Teilen → «Zum Home-Bildschirm».
- Ohne Internet werden die zuletzt geladenen Daten angezeigt.

## Dateien

| Datei | Zweck |
|---|---|
| `index.html` | Die ganze App (Darstellung, Kleidungslogik, Datenabruf) |
| `manifest.webmanifest` | App-Name, Farben und Symbole für den Home-Bildschirm |
| `apple-touch-icon.png` | App-Symbol fürs iPhone (180 × 180) |
| `icon-512.png` | App-Symbol in gross (512 × 512) |

## Installation auf einem Webserver

Die App braucht nur einen Webserver, der statische Dateien über **HTTPS** ausliefert. Alle Pfade sind relativ, sie läuft also auch in einem Unterordner (z. B. `https://phytonetzwerk.ch/wetter/`).

### Variante A: Plesk (z. B. Kreativmedia)

1. In Plesk **Dateien** öffnen und in `httpdocs` einen Ordner `wetter` anlegen.
2. Die vier Dateien oben hochladen (README und `.git` sind nicht nötig).
3. Unter **SSL/TLS** ein Let's-Encrypt-Zertifikat aktivieren, falls noch keines besteht.

### Variante B: Plesk mit Git-Anbindung

Plesk kann ein Repository direkt beziehen: **Websites & Domains → Git → Repository hinzufügen**, die URL dieses Repositorys eintragen und als Zielordner `httpdocs/wetter` wählen. Spätere Änderungen werden dann per «Pull» übernommen.

### Variante C: per Kommandozeile auf einem Server mit Git

```bash
cd /pfad/zu/httpdocs
git clone <repository-url> wetter
```

Aktualisieren: `git -C wetter pull`

## Lokal testen

```bash
python -m http.server 8790
```

Dann `http://localhost:8790` im Browser öffnen.

## Anpassen

- **Orte ändern oder ergänzen:** Liste `PLACES` oben im Skript in `index.html` (Name, Koordinaten, Höhenangabe, Wind- und Nebelhinweis) und dazu passend eine `<option>` im Auswahlmenü `#place`. Die Haltestelle für den Fahrplan steht in `STOPS` (SBB-Nummer, z. B. über `https://transport.opendata.ch/v1/locations?query=Ortsname` zu finden).
- **Kleidungsregeln:** Funktion `recommend()` in `index.html`. Die Grenzen richten sich nach der kältesten gefühlten Temperatur im gewählten Zeitraum.
