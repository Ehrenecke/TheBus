# The Bus – AMP-Templates für den Dedicated Server

AMP-(CubeCoders)-Game-Templates für den **The Bus Dedicated Server** auf Linux.
Der Server ist eine Windows-Anwendung und läuft unter AMP über Wine.
Public (Stable) und Beta sind zwei getrennte Templates, die gleichzeitig auf einem Host bestehen können.

Autor: **Ehrenecke** · Inoffiziell, nicht mit CubeCoders oder den Entwicklern von The Bus verbunden.

## Was die Templates machen

| Vorlage in AMP | Steam-Branch | Installationsordner |
|---|---|---|
| Custom / The Bus | `public` (Stable) | `./the-bus/` |
| Custom / The Bus (Beta) | `beta` | `./the-bus-beta/` |

- **Installation und Updates:** per SteamCMD, Steam-App **507320** (The Bus – Dedicated Server, kostenlos, anonymer Login). Geladen wird das Windows-Depot. Die Parent-App ist 491540 (das Spiel).
- **Branch:** Jede Vorlage hat ihren Branch fest hinterlegt. Es gibt keinen Umschalter, stattdessen wählst du beim Erstellen der Instanz die passende Vorlage.
- **Start:** `wine "./TheBus/Binaries/Win64/TheBusServer.exe"` mit dem AMP-Standard-Wine-Image `cubecoders/ampbase:wine`. Es werden weder Xvfb noch ein virtuelles Display benötigt.
- **Startparameter:** `-log -useperfthreads -port=<Game Port> -QueryPort=<Query Port>`, dazu optional eigene Zusatzparameter.
- **Wine:** Der Prefix liegt unter `<Instanz>/.wine` (64 Bit). Wine legt ihn beim ersten Start selbst an, das dauert beim ersten Mal etwas länger. `WINEDLLOVERRIDES=mscoree,mshtml=` verhindert Mono-/Gecko-Installationsdialoge.
- **Konsole und Status:** AMP liest die Unreal-Logdatei `TheBus/Saved/Logs/TheBus.log` (TailLogFile, wie beim offiziellen Farming-Simulator-Template). Der Status ist sofort nach dem Start „Bereit“. Ob der Server wirklich läuft, zeigt die Zeile `Server can be reached with the GUID` in der Konsole.
- **Stoppen:** `SIGTERM`, Timeout 30 s. Der Server wird damit hart beendet. Hängt ein Stop, hilft **Kill** in AMP.
- **Verzeichnisse:** Beim Update werden `TheBus/Saved/Config/WindowsServer/` und `TheBus/Saved/Logs/` angelegt.

## Ports

| Port | Protokoll | Zweck |
|---|---|---|
| 7777 | UDP | Game Port |
| 27015 | UDP | Query Port |

Beide Ports in der Firewall freigeben. Weitere eingehende Ports sind nicht dokumentiert. Die Verbindung zum Server läuft per GUID über Epic Online Services (ausgehend).

## Einstellungen in AMP

- **Zusätzliche Startparameter:** freie Unreal-Parameter, die an `TheBusServer.exe` angehängt werden.
- **Config-Dateien:** Die Unreal-Standarddateien `GameUserSettings.ini`, `Game.ini` und `Engine.ini` aus `TheBus/Saved/Config/WindowsServer/` erscheinen in der AMP-Konfigurationsverwaltung, sobald der Server sie erzeugt hat. Dass The Bus seine eigenen Einstellungen dort speichert, ist nicht belegt.
- **Servername, Passwörter, Max-Spieler:** laut Dokumentation über das Admin-Menü im Spiel bzw. Server-Befehle, nicht über Startparameter. Die angezeigte Spielerzahl (10) ist nur der Standardwert und wird nicht an den Server übergeben.

## Dateien

| Datei | Zweck |
|---|---|
| `manifest.json` | Repository-Beschreibung, Pflicht für AMP |
| `thebus.kvp` | Template Public |
| `thebusconfig.json`, `thebusmetaconfig.json`, `thebusports.json`, `thebusupdates.json` | Einstellungen, Config-Dateien, Ports, Updates (Public) |
| `thebus-beta.kvp` | Template Beta |
| `thebus-betaconfig.json`, `thebus-betametaconfig.json`, `thebus-betaports.json`, `thebus-betaupdates.json` | dasselbe für Beta |

Alle Dateien müssen kleingeschrieben im Repository-Root liegen, ohne Unterordner.

## Installation in AMP

1. Auf dem AMP-Server muss **Git** installiert sein (`git --version`). Nach der Installation AMP bzw. den Server neu starten.
2. In AMP: Instanz **ADS01** → *Configuration* → *Instance Deployment* → *Configuration Repositories* → **Add**:
   ```
   Ehrenecke/TheBus:main
   ```
3. **Fetch Latest** klicken, Browser neu laden (Strg+F5).
4. *Create Instance* → „Custom / The Bus“ oder „Custom / The Bus (Beta)“ wählen.
5. Nach dem Anlegen **Update** klicken (lädt ca. 3,8 GB), danach starten.

Spätere Änderungen am Template übernehmen bereits erstellte Instanzen nicht. Instanz neu erstellen.

## Wenn die Vorlage nicht erscheint

AMP meldet fehlerhafte Repositories nicht, es lässt sie still weg.
- `git --version` auf dem Server prüfen.
- Das Repository muss öffentlich sein, die Dateien direkt im Root liegen und exakt so heißen wie oben.
- Im Verzeichnis `~amp/.ampdata/instances/ADS01/Plugins/ADSModule/DeploymentTemplates/` muss ein Ordner `Ehrenecke-TheBus-main` mit `manifest.json` und `thebus.kvp` liegen.
- Alternative ohne Git: Ordner `LOCAL-thebus-main` im selben Verzeichnis anlegen, die Dateien hineinkopieren, `chown -R amp:amp LOCAL-thebus-main`, ADS neu starten.

## Bekannte Einschränkungen

- Es gibt bei App 507320 auch ein Linux-Depot (507322). Die Templates nutzen bewusst das Windows-Depot mit Wine.
- Der Name der Logdatei (`TheBus.log`) ist angenommen. Heißt sie anders, `App.TailLogFilePath` in den `.kvp` anpassen.
- `-QueryPort` ist ein Unreal-Standardparameter. Ob The Bus ihn auswertet, ist nicht dokumentiert.
- Der Server kann nur hart beendet werden (SIGTERM). Ob dabei noch sauber gespeichert wird, ist ungeprüft.
- Die Templates sind auf x86_64-Hosts ausgelegt.
