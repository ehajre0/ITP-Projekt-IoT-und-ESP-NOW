# ITP-Projekt-IoT-und-ESP-NOW
# ESP32 Parksensor mit ESP-NOW, OLED, LED, Buzzer und Webinterface

## Projektbeschreibung
Dieses Projekt realisiert einen intelligenten Parksensor mit zwei ESP32-Boards. 
Ein Sender misst mit einem HC-SR04 Ultraschallsensor die Entfernung zu einem Objekt. 
Die Daten werden per ESP-NOW an einen Receiver übertragen. 
Der Receiver verarbeitet die Daten und zeigt sie über OLED-Display, RGB-LED, Buzzer sowie ein Webinterface mit API an.

## Funktionen
- Distanzmessung mit HC-SR04
- Filterung ungültiger Messwerte
- Datenübertragung per ESP-NOW
- Anzeige auf OLED-Display
- Statusanzeige über RGB-LED
- Akustische Warnung über Buzzer
- Webinterface zur Anzeige im Browser
- API für Messdaten

## Hardware
- 2x ESP32 Dev Module
- HC-SR04 Ultraschallsensor
- OLED Display SSD1306
- RGB-LED
- Buzzer
- Breadboard
- Widerstände
- Jumper-Kabel

## Verdrahtung

### Sender
- VCC -> 5V
- GND -> GND
- TRIG -> GPIO 5
- ECHO -> GPIO 18 (über Spannungsteiler)

### Receiver
- OLED SDA -> GPIO 21
- OLED SCL -> GPIO 22
- OLED VCC -> 3.3V
- OLED GND -> GND
- LED R -> GPIO 25
- LED G -> GPIO 26
- LED B -> GPIO 27
- Buzzer -> GPIO 14

## Systemaufbau
HC-SR04 -> ESP32 Sender -> ESP-NOW -> ESP32 Receiver -> OLED / LED / Buzzer / Website / API

## Ablauf
1. Der Sender misst die Entfernung.
2. Werte außerhalb von 2 bis 400 cm werden verworfen.
3. Mehrere Messungen werden gemittelt.
4. Der Sender sendet die Daten per ESP-NOW.
5. Der Receiver verarbeitet die Distanz.
6. OLED, LED, Buzzer und Website zeigen den Zustand an.

## Alarmregel
- Grün = sicher
- Rot = Objekt zu nah
- Gelb = keine gültigen Daten / Messung läuft

## API

### GET /api/data
```json
{
  "distanceCm": 35.4,
  "unit": "cm",
  "valid": true,
  "rxOk": true,
  "state": "ALARM"
}
