# ITP Projekt – ESP32 Parksensor mit ESP-NOW

## Gruppenmitglieder

- Bojan  
- Erdi  

---

## Projektbeschreibung

In diesem Projekt wurde ein intelligentes Parksensorsystem mit zwei ESP32-Boards umgesetzt.

Der erste ESP32 fungiert als **Sender** und misst mit einem **HC-SR04 Ultraschallsensor** die Entfernung zu einem Objekt. Die Messwerte werden gefiltert und per **ESP-NOW** an einen zweiten ESP32 übertragen.

Der zweite ESP32 fungiert als **Empfänger**. Er verarbeitet die Daten und stellt sie über mehrere Ausgabekanäle dar:

- RGB-LED  
- Buzzer  
- Webinterface  
- API  

---

## Funktionen

- Distanzmessung mit Ultraschallsensor  
- Filterung ungültiger Messwerte (2–400 cm)  
- Mittelwertbildung für stabile Werte  
- Drahtlose Übertragung mit ESP-NOW  
- Statusanzeige über RGB-LED  
- Akustische Warnung mit Buzzer (Intervall + Dauerton)  
- Webinterface mit Live-Daten  
- REST-API (JSON)  
- Speicherung der letzten Messungen  

---

## Verwendete Komponenten

- 2x ESP32 Dev Module  
- HC-SR04 Ultraschallsensor  
- RGB-LED  
- Buzzer  
- Breadboard und Jumper-Kabel  
- Widerstände  
- Spannungsteiler für ECHO-Pin  

---

## Systemaufbau

```text
HC-SR04 → ESP32 Sender → ESP-NOW → ESP32 Receiver
                                      |
                     -------------------------------------
                     |          |         |        |
                   LED       Buzzer    Website    API
