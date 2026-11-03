# spit

[English](../README.md) · [한국어](README.ko.md) · [日本語](README.ja.md) · [简体中文](README.zh-CN.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Português](README.pt.md)

Eine kleine Tray-App für Windows und macOS, die schnelle einzeilige Notizen lokal als Markdown speichert. Das schlichte Eingabefenster bleibt vorgeladen; Dateien werden im Hintergrund geschrieben. Maßgeblich ist die englische Anleitung.

Das Quellcode-Repository ist privat. Das [öffentliche Distributions-Repository](https://github.com/dding-g/spit-releases) enthält ausschließlich offizielle Installationsdateien, Updates und Benutzeranleitungen.

## Installation und Bedienung

Lade ein veröffentlichtes Archiv für dein System und deine CPU unter [Releases](https://github.com/dding-g/spit-releases/releases) herunter und entpacke es. [Website](https://spit-ashy.vercel.app). Kopiere unter macOS 12 oder neuer `spit.app` nach Applications. Starte unter Windows `spit.exe` aus einem beliebigen Ordner; Microsoft WebView2 Runtime wird benötigt. Kein Installationsprogramm oder automatischer Anmeldestart. macOS hat nur eine Ad-hoc-Signatur, **keine Developer-ID-Signatur oder Notarisierung**; Windows hat keine Authenticode-Signatur. Gatekeeper oder SmartScreen können warnen oder blockieren. Öffne nur vertrauenswürdige Downloads.

Wähle `macos-arm64` für Apple silicon oder `macos-amd64` für Intel; unter Windows `windows-amd64` für x64 oder `windows-arm64` für ARM64. **Ausführung sowie Update/Neustart wurden nicht unter Windows geprüft**; Cross-Builds sind keine Laufzeitprüfung.

Beim ersten Start erkennt spit registrierte Obsidian-Vaults ohne rekursive Suche auf der gesamten Festplatte und verwendet den zuletzt genutzten Vault. Falls keiner vorhanden ist, wähle in den Einstellungen einen Ordner. Jeder vorhandene Ordner ist geeignet; Obsidian ist nicht erforderlich.

- macOS `Cmd + \` / Windows `Ctrl + \`: schnelle Notizeingabe ein- oder ausblenden.
- `Enter`: sofort ausblenden und zum Speichern im Hintergrund absenden. Während einer IME-Komposition wird nicht abgesendet.
- `Esc`: ausblenden und den ungesendeten Entwurf **nur während dieser App-Sitzung** behalten. Normales Beenden und erneutes Starten stellt ihn nicht wieder her; Ausnahme ist der unten beschriebene ausdrückliche Update-Neustart.
- Mehrzeiliger eingefügter Text wird zu einer Zeile. Leere Notizen werden ignoriert; maximal 10.000 UTF-16-Codeeinheiten.
- Ein Linksklick auf das Tray-/Menüleistensymbol öffnet oder schließt den **schreibgeschützten Verlauf**, zunächst für heute. Alle gespeicherten Tage sind auswählbar; du kannst zum vorherigen/nächsten gespeicherten Tag wechseln oder aktualisieren. Kein Bearbeiten, Löschen oder Suchen und kein dauerhaftes Desktop-/System-Widget.
- Das Tray-Menü öffnet ebenfalls Verlauf, Neue Notiz und Ordner- und Spracheinstellungen. Nutze Neue Notiz, wenn das globale Tastenkürzel nicht registriert werden kann. Fenster schließen beendet die App nicht; wähle dazu Beenden.

Die Sprache wird **automatisch anhand der System-/Browsersprache erkannt** oder manuell gewählt und gespeichert: Englisch, Koreanisch, Japanisch, vereinfachtes Chinesisch, Spanisch, Französisch, Deutsch oder Portugiesisch. Die manuelle Auswahl hat Vorrang; nicht unterstützte Sprachen und fehlende Übersetzungen fallen auf Englisch zurück. Beim Sprachwechsel bleibt der aktuelle Entwurf erhalten.

## Updates und Datenschutz

spit sucht auf GitHub Releases nach Updates und lädt sie automatisch im Hintergrund herunter. Die Einstellungen zeigen Status, Fehler, manuelle Prüfung und bei Bereitschaft den Neustart. **Installation und Neustart erfolgen nur nach deinem Klick auf Neustart**; die Eingabe wird nie automatisch unterbrochen. Laufende Speichervorgänge und die dauerhafte Sicherung der Warteschlange werden abgewartet; während einer IME-Komposition wird der Neustart verweigert. Der ungesendete Entwurf wird nur für das Update gesichert und danach wiederhergestellt. Normale Sitzungsentwürfe werden dadurch nicht dauerhaft gespeichert.

Vor der Installation werden SHA-256 und die Ed25519-Signatur mit dem fest eingebauten öffentlichen Schlüssel geprüft; ein Fehler blockiert die Installation. Diese Signatur ersetzt weder OS-Codesignierung noch Apple-Notarisierung oder Windows-Herausgebervertrauen. Update-Prüfungen und Downloads kontaktieren GitHub und dessen Download-Hosts, laden aber keine Notizen oder Vault-Dateien hoch. Die Vault-Synchronisierung richtet sich nach anderer Software.

## Dateien und Wiederherstellung

Bei Auswahl von `chosenRoot` wird nach `chosenRoot/admo/YYYY-MM-DD.md` geschrieben. Heißt der gewählte Ordner bereits `admo`, wird er direkt verwendet, ohne `admo/admo`. Vorhandene Tagesnotizen im Stammordner werden nicht verschoben. Auch bei verzögertem Speichern gelten das lokale Datum und die Uhrzeit des Absendens:

```markdown
- 09:12:04 Milch kaufen <!-- admo:UUID -->
```

Der ausgeblendete HTML-Kommentar kennzeichnet die Notiz zur Duplikatvermeidung bei der Wiederherstellung; im Quelltext ist er sichtbar. Text wird maskiert statt als Markdown/HTML interpretiert, vorhandener Dateiinhalt bleibt erhalten. Der Verlauf liest datierte Dateien im tatsächlichen `admo`-Ordner, nicht im gesamten Vault, und erkennt unterstützte ältere Notizmarkierungen.

Abgesendete Notizen werden zuerst in der lokalen Warteschlange `state.json` gesichert, dann an Markdown angehängt und synchronisiert und erst danach aus der Warteschlange entfernt. Bei Schreibfehlern bleiben sie erhalten. Einstellungen und Tray zeigen den Fehler; alle **5 Sekunden** sowie beim Neustart wird erneut versucht. Scheitert bereits das lokale Sichern der Warteschlange, kehrt der Text in den Entwurf zurück.

Ausstehende Notizen behalten ihr ursprüngliches Ziel; ein Ordnerwechsel betrifft nur neue Notizen. Die Verarbeitung erfolgt der Reihe nach: Ein nicht verfügbares früheres Ziel blockiert spätere Notizen. Stelle Ordner oder Zugriffsrechte wieder her, damit der nächste Versuch gelingt. Ein beschädigter Zustand wird **nicht automatisch zurückgesetzt**: Sichere die in der Fehlermeldung genannte Datei vor der Reparatur.

Lokaler Zustand, getrennt vom Markdown-Ordner:

- macOS: `~/Library/Application Support/spit/state.json`
- Windows: `%AppData%\spit\state.json`

Am Standardort wird bei fehlendem `spit`-Zustand zuerst alter `admo`-, dann `adhd-memo`-Zustand migriert; vorhandener neuer Zustand wird nie überschrieben. Markdown-Ordner und bestehende Markierungen bleiben aus Kompatibilitätsgründen `admo`. Vault-Ordner werden nicht umbenannt, verschoben oder gelöscht; ausstehende Notizen behalten ihre Ziele.

Notizen und Zustand sind **unverschlüsselter Klartext**. Ein ausgeblendetes Eingabefenster bestätigt keine dauerhafte Speicherung: Erzwungenes Beenden oder Stromausfall vor dem Sichern der Warteschlange kann diese Eingabe verlieren. Unterbrochenes Anhängen kann unvollständige Zeilen hinterlassen; die Wiederherstellung bewahrt sie und ergänzt die vollständige Notiz. Dateisystem-/Gerätefehler und gleichzeitige Änderungen durch Editoren oder Cloud-Synchronisierung liegen außerhalb der Garantie.
