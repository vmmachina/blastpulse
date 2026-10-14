# Blast Pulse - by Stefan Gourguis

[English](https://github.com/vmmachina/blastpulse/blob/main/README.md) | [Deutsch](https://github.com/vmmachina/blastpulse/blob/main/README.de.md)

Horizon-Blast-Verbindungsdaten direkt auf dem Desktop im Blick.

Dieses Repository stellt die fertige Anwendung und die Benutzeranleitungen bereit. Der Anwendungsquellcode wird hier nicht angeboten. Zum Download das unten verlinkte Windows-ZIP oder die EXE verwenden.

Die Idee entstand aus einer einfachen Unannehmlichkeit: Um die Verbindung zu prüfen, musste man immer wieder das ursprüngliche Horizon-Blast-Symbol unten rechts im Infobereich öffnen. BlastPulse hält die relevanten Informationen direkt auf dem Desktop sichtbar, damit sich die Sitzung während der Arbeit beobachten lässt.

Die kompakte Anzeige zeigt Verbindungsdetails sowie Blast-FPS, Antwortzeit (RTT) und Uplink-Paketverlust. Live-Zähler werden standardmässig jede Sekunde aktualisiert. Die transparente Anzeige liegt unten rechts auf dem primären Desktop unter normalen Anwendungsfenstern. Das Hintergrundbild bleibt erhalten und wird nicht bei jeder Aktualisierung neu geschrieben.

## Herunterladen und starten

[BlastPulse 1.0 für Windows x64 (ZIP)](https://github.com/vmmachina/blastpulse/releases/download/v1.0.0/BlastPulse-1.0.0-win-x64.zip) herunterladen. Das Paket enthält die Anwendung, eine Beispielkonfiguration und die Dokumentation auf Englisch und Deutsch. Zum Offline-Lesen das ZIP entpacken und `README.html` im Browser öffnen; die Links English/Deutsch oben wechseln die Sprache.

Auf der [Release-Seite](https://github.com/vmmachina/blastpulse/releases/tag/v1.0.0) stehen auch die [einzelne EXE](https://github.com/vmmachina/blastpulse/releases/download/v1.0.0/BlastPulse.exe), [config.json](https://github.com/vmmachina/blastpulse/releases/download/v1.0.0/config.json) und [SHA-256-Prüfsummen](https://github.com/vmmachina/blastpulse/releases/download/v1.0.0/SHA256SUMS.txt) bereit.

Voraussetzungen sind Windows 10/11 x64, .NET Framework 4.x und eine aktive Horizon-Blast-Sitzung mit lesbaren Sitzungszählern und Logs. Die Anwendung ist unsigniert.

1. ZIP in einen lokalen Ordner oder auf eine erreichbare Netzwerkfreigabe entpacken.
2. `BlastPulse.exe` innerhalb der Horizon-Blast-Sitzung starten.
3. Die Desktop-Anzeige erscheint und aktualisiert sich automatisch.

Ein Rechtsklick auf das BlastPulse-Symbol im Infobereich neben der Uhr öffnet **Language/Sprache → English / Deutsch**, **Desktop-Anzeige ausblenden/einblenden** und **BlastPulse beenden**. Englisch ist die Standardsprache. Die Sprachwahl gilt sofort und wird für den jeweiligen Windows-Benutzer gespeichert.

## Anzeige anpassen

`config.json` neben `BlastPulse.exe` ablegen und BlastPulse nach Änderungen neu starten. Ohne diese Datei gelten die folgenden Standardwerte.

| Einstellung | Standard | Zweck und zulässige Werte |
| --- | --- | --- |
| `RefreshSeconds` | `1` | Aktualisierung der Live-Zähler, 1–3600 Sekunden |
| `LogRefreshSeconds` | `5` | Aktualisierung der Log- und Bildschirmangaben, 1–3600 Sekunden |
| `PanelWidth` | `640` | Breite der Anzeige, 460–900 Pixel |
| `Margin` | `30` | Abstand zum rechten Rand, 12–150 Pixel |
| `BottomOffset` | `150` | Freiraum unter der Anzeige innerhalb der Desktop-Arbeitsfläche, 0–1000 Pixel |
| `FontSize` | `11` | Schriftgrösse, 10–22 Punkt |
| `FontFamily` | `"Segoe UI"` | Installierte Windows-Schrift; andernfalls verwendet Windows eine Ersatzschrift |
| `Language` | `"en"` | Anfängliche Sprache: `"en"` oder `"de"` |

Der voreingestellte Abstand nach unten lässt Platz für vorhandenen BGInfo-Text. Für eine schmalere Anzeige direkt oberhalb der Taskleiste genügt diese vollständige `config.json`; ausgelassene Einstellungen behalten ihre Standardwerte:

```json
{
  "PanelWidth": 460,
  "Margin": 20,
  "BottomOffset": 0,
  "FontSize": 11
}
```

Die im Tray gewählte Sprache steht in `%LOCALAPPDATA%\BlastPulse\preferences.json` und hat Vorrang vor `Language` in der gemeinsamen Konfiguration. Um zur konfigurierten Sprache zurückzukehren, BlastPulse beenden, diese Einstellungsdatei entfernen und die Anwendung erneut starten. Für `config.json` genügt Lesezugriff, auch auf einer Netzwerkfreigabe.

## Start von einer Freigabe oder per GPO

`BlastPulse.exe` und `config.json` gemeinsam auf der Freigabe ablegen. Benutzer benötigen Lesen/Ausführen. Für den Start bei der Anmeldung kann ein **Benutzer-Anmeldeskript** diesen Aufruf enthalten:

```bat
start "" "\\example.com\SYSVOL\example.com\scripts\BlastPulse\BlastPulse.exe"
```

Den Beispielpfad durch den eigenen UNC- oder lokalen Pfad ersetzen. BlastPulse bleibt in der Benutzersitzung aktiv; das Anmeldeskript sollte deshalb nicht auf das Ende der Anwendung warten. Die Anwendung richtet selbst keinen Autostart ein. Bestehendes BGInfo kann parallel laufen; mit `BottomOffset` lässt sich genügend Platz dafür freihalten.

Für ein Update BlastPulse beenden, die EXE ersetzen und erneut starten. Die bestehende `config.json` beibehalten, damit die Positionierung erhalten bleibt. Vor einer breiteren Verteilung den Freigabezugriff und die Anwendungsrichtlinien der eigenen Organisation prüfen.

## Datenquellen

BlastPulse liest die Daten lokal auf dem Windows-Desktop. Es fragt keine Horizon-Connection-Server-API ab und ruft die interne `wavedll.dll`-Schnittstelle des Tray-Tools nicht auf. BlastPulse benötigt keine Zugangsdaten und lädt die ausgelesenen Werte nicht zu einem externen Dienst hoch.

| Angezeigte Information | Quelle |
| --- | --- |
| RTT und Uplink-Paketverlust | `Win32_PerfFormattedData_Counters_HorizonBlastSessionCounters`: `RTT`, `PacketLossUplink` |
| Blast-FPS | `Win32_PerfFormattedData_Counters_HorizonBlastImagingCounters`: `Framespersecond` |
| Codec-Name und Bildschirmaufnahme | Letzte passende Einträge `Created region encoder` und `Screen capture name` im Blast-Worker-Log der Sitzung |
| Horizon-Qualitätsstatus, Score und Transport-Backend | Letzter Eintrag `BlastSocket_GetVvcQualityIndicators`: `qualityState`, `qualityScore`, `Backend` und der Zeitstempel des Eintrags |
| Display-Treiber, Auflösung und Bildwiederholrate | `Win32_VideoController`, gefiltert auf Geräte mit `Horizon` im Namen |
| Position und DPI des primären Bildschirms | Windows-Bildschirmkoordinaten und DPI der Desktop-Grafik |

Beide Leistungszählerklassen werden über WMI im Namensraum `root\cimv2` gelesen. BlastPulse wählt die Zählerinstanz, deren `Name` mit `Session ID: <aktuelle Sitzungs-ID>;` beginnt.

Die kompakte Desktop-Anzeige stellt die sechs ursprünglichen Verbindungsangaben dar: Codec, Aufnahmeverfahren, Anzeigegerät, Bildschirmkonfiguration, Transport und Horizon-Qualität. Darunter stehen Blast-FPS, RTT und Uplink-Paketverlust. **Blast-FPS beschreiben die Desktop-Übertragung, nicht die Wiedergabe-Bildrate des Quellvideos.**

Die Logdatei liegt unter:

```text
%ProgramData%\Omnissa\Horizon\Logs\Blast-Worker-SessionId<Sitzungs-ID>.log
```

Gelesen werden höchstens die letzten 4 MiB. Logwerte stammen aus dem jeweils letzten passenden Eintrag dieses Abschnitts und können älter als die aktuellen Leistungszähler sein. Der Codec-Name stammt direkt aus dem Log; eine Zuordnung numerischer Encoder-IDs wird nicht angenommen.

Der originale Qualitätsstatus hat einen eigenen Zeitstempel. Er hängt von einem passenden Agent-Logeintrag ab; BlastPulse fordert keine Aktualisierung beim ursprünglichen Tray-Tool an. Eine kontinuierliche Aktualisierung des Originalstatus ist nicht garantiert. Ein mehr als 90 Sekunden alter Originalstatus wird als älterer Logstand gekennzeichnet. BlastPulse gibt keine selbst berechnete Bewertung als Horizon-Status aus.

Fehlt im aktuellen Logabschnitt ein passender Qualitäts-Eintrag, zeigen Transport und Qualität **Nicht gemeldet**. Die Fußzeile lautet **Qualitätsstatus: Kein passender Logeintrag**. Das betrifft diesen einzelnen Eintrag; Codec-/Aufnahmeangaben aus dem Log und Live-Zähler können trotzdem verfügbar sein. Bei erkanntem Eintrag steht dessen eigene Meldezeit; ein nicht erkannter Zeitstempel wird gesondert ausgewiesen.

Der angezeigte Verbindungstransport stammt aus dem Log. Er wird nicht aus der momentanen Datenverkehrsaktivität abgeleitet.

## Kompatibilität

BlastPulse 1.0 für Windows x64.

- Die Anzeige nutzt den primären Bildschirm. Mehrere Monitore, Bildschirm-/DPI-Wechsel, Explorer-Neustarts und Sitzungswiederverbindungen wurden noch nicht umfassend getestet.
- Welche Angaben verfügbar sind, hängt von Horizon-Agent-Version, Logformat und den Zugriffsrechten auf Zähler und Logs ab.
- Eine zentrale Verteilung vor dem Rollout in der eigenen Umgebung testen.

## Herstellerreferenzen

- [Omnissa: Blast-Leistungszähler](https://docs.omnissa.com/de-DE/Horizon-Remote-Desktop-Features-V2606/ExaminingBlastSessionStatisticswithWindowsPerformanceMonitor)
- [Omnissa: Blast-Logs und Paketverlust](https://kb.omnissa.com/s/article/89455)
