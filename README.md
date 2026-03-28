# ITP-Projekt-IoT-und-ESP-NOW
# ITP Projekt – ESP32 Parksensor mit ESP-NOW, OLED und Webinterface

## Gruppenmitglieder

- Bojan
- Erdi

## Varianten
- Variante 2: Status LED
- Variante 4: API

## Projektbeschreibung

In diesem Projekt wurde ein intelligentes Parksensorsystem mit zwei ESP32-Boards aufgebaut.

Der erste ESP32 fungiert als **Sender**. Er misst mit einem **HC-SR04-Ultraschallsensor** die Entfernung zu einem Objekt, bereinigt die Messwerte und sendet die Daten per **ESP-NOW** an einen zweiten ESP32.

Der zweite ESP32 fungiert als **Empfänger / Receiver**. Er empfängt die Distanzdaten, wertet sie aus und stellt sie über mehrere Ausgabekanäle dar:

- **OLED-Display**
- **RGB-LED**
- **Buzzer**
- **Webinterface**
- **API**

Dadurch kann der aktuelle Abstand sowohl lokal am System als auch über den Browser überwacht werden.

## Umgesetzte Funktionen

- Distanzmessung mit Ultraschallsensor
- Filterung ungültiger Messwerte
- Datenübertragung per ESP-NOW
- Visualisierung auf OLED-Display
- Statusanzeige über RGB-LED
- Akustische Warnung über Buzzer
- Webinterface zur Live-Anzeige
- API für JSON-Daten
- Speicherung der letzten Messungen im Arbeitsspeicher des Receivers

## Verwendete Komponenten

Siehe: [Komponentenliste](docs/komponenten.md)

## Funktionsweise

1. Der Sender-ESP32 misst die Entfernung mit dem HC-SR04-Ultraschallsensor.
2. Es werden mehrere Messungen durchgeführt und gemittelt.
3. Werte außerhalb des gültigen Messbereichs werden verworfen.
4. Der bereinigte Messwert wird per ESP-NOW an den Empfänger gesendet.
5. Der Empfänger verarbeitet die Daten und bestimmt den aktuellen Systemstatus.
6. Die Entfernung wird auf dem OLED-Display angezeigt.
7. Die RGB-LED zeigt den Zustand farblich an.
8. Der Buzzer warnt abhängig von der Entfernung mit verschiedenen Piep-Intervallen.
9. Zusätzlich werden die Daten über ein Webinterface und eine API bereitgestellt.

## Messwertverarbeitung

Die Distanzmessung arbeitet im Bereich von **ca. 2 cm bis 400 cm**.

- Werte **unter 2 cm** oder **über 400 cm** werden als ungültig verworfen.
- Für stabilere Ergebnisse werden mehrere Einzelmessungen aufgenommen und gemittelt.
- Jede gültige Messung wird mit Statusinformationen weiterverarbeitet.

## Alarmregel

Die Alarmregel legt fest, ab welcher Entfernung ein Warnsignal ausgelöst wird.

Beispielhafte Konfiguration:

- Mindestentfernung: **40 cm**
- Sicherheitsbereich / Hysterese: **5 cm**
- Alarm aktiviert: **ja**

### Verhalten des Buzzers

- **weit entfernt** -> kein Ton
- **näher** -> langsames Piepen
- **noch näher** -> schnelleres Piepen
- **kritischer Bereich** -> Dauerton

Dadurch verhält sich das System ähnlich wie ein echter Parksensor im Auto.

## Statusanzeige

### RGB-LED

- **Grün** = Abstand im sicheren Bereich
- **Rot** = Objekt zu nah / Alarm
- **Gelb** = Messung läuft oder es liegen keine gültigen Daten vor

### OLED-Display

Auf dem OLED werden angezeigt:

- aktuelle Entfernung in cm
- aktueller Status
- Warnmodus / Piepverhalten

## Systemstatus

Der Receiver verwaltet einen allgemeinen Systemstatus zur Anzeige im OLED und Webinterface.

Mögliche Zustände:

- **OK**
- **ALARM**
- **FEHLER**

Zusätzlich wird gespeichert:

- Zeitpunkt der letzten empfangenen Messung
- Zeitpunkt der letzten gültigen Messung
- Gültigkeit des aktuellen Messwerts

## Webinterface

Das Webinterface ist über die IP-Adresse des Receiver-ESP32 erreichbar und zeigt live:

- aktuelle Entfernung in cm
- aktuellen Status
- Alarmzustand
- letzte gültige Messung
- IP-Adresse des Receivers
- verwendeten WLAN-Kanal
- letzte empfangene Messungen

## API

Die Daten können zusätzlich als JSON über folgende Endpunkte abgerufen werden:

### `GET /api/data`

Liefert die aktuellen Messdaten und den Systemstatus.

Beispiel:

```json
{
  "distanceCm": 35.4,
  "unit": "cm",
  "valid": true,
  "rxOk": true,
  "state": "ALARM",
  "alarmEnabled": true,
  "alarmActive": true
}
```

### `GET /api/config`

Liefert die aktuelle Alarmkonfiguration.

### `GET /api/history?n=8`

Liefert die letzten Messungen.

### `GET /api/set?enabled=1&min=40&margin=5`

Ändert die Alarmregel direkt über den Browser.

## Verdrahtung

### Sender

#### HC-SR04 -> ESP32 Sender

- VCC -> 5V
- GND -> GND
- TRIG -> GPIO 5
- ECHO -> GPIO 18 (**über Spannungsteiler**)

### Receiver

#### RGB-LED / Buzzer -> ESP32 Receiver

- LED R -> GPIO 25
- LED G -> GPIO 26
- LED B -> GPIO 27
- Buzzer -> GPIO 14

## Projektstruktur

```text
esp32-parksensor/
├── code/
│   ├── sender.ino
│   └── receiver.ino
├── docs/
│   ├── receiver_esp32.jpg
│   ├── sender_esp32.png
│   ├── projekt_schaltung.png
│   └── website.png
└── README.md
```

## Inbetriebnahme

1. Receiver-Code auf den Receiver-ESP32 laden
2. Serial Monitor öffnen
3. MAC-Adresse des Receivers notieren
4. Die Receiver-MAC im Sender-Code eintragen
5. Sender-Code auf den Sender-ESP32 laden
6. Beide ESP32 mit dem gleichen Hotspot / WLAN verbinden
7. Die IP-Adresse des Receivers im Browser öffnen
8. Messwerte auf OLED, LED, Buzzer und Website prüfen

## Dateien im Projekt

- `code/sender_esp32.ino`
- `code/receiver_esp32.ino`
- `README.md`

## Fotos und Schaltplan

Siehe Ordner:

- `docs/`

## Herausforderungen und Lösungen

### Herausforderung 1: ESP-NOW + WLAN gleichzeitig
Da das Webinterface aktiv ist, mussten Sender und Receiver auf demselben WLAN-Kanal arbeiten.  
Die Lösung war, beide ESP32 mit demselben Hotspot zu verbinden.

### Herausforderung 2: Ungültige Messwerte
Der Ultraschallsensor liefert teilweise fehlerhafte Werte.  
Diese wurden durch Messwertfilterung und Mittelwertbildung bereinigt.

### Herausforderung 3: Mehrere Ausgabekanäle gleichzeitig
OLED, LED, Buzzer, Webinterface und API mussten parallel funktionieren.  
Dies wurde durch eine klare Aufteilung zwischen Sender und Receiver umgesetzt.

## Fazit

Das Projekt zeigt, wie mit zwei ESP32-Boards ein vollständiges IoT-System aufgebaut werden kann.  
Die Distanz wird drahtlos per ESP-NOW übertragen und lokal wie auch im Browser angezeigt.  
Durch OLED, LED, Buzzer, Webinterface und API entsteht ein vielseitiges und praxisnahes System mit typischem Anwendungsbezug aus dem Bereich Assistenz- und Sensorsysteme.
