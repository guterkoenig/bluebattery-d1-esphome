# BlueBattery D1 (`V4.19`) via ESPHome

Private ESPHome-Konfiguration zum Auslesen eines **BlueBattery D1** (`BlueBasic BlueBattery-D1`, Firmware `V4.19`) über Bluetooth Low Energy (`BCLive`) auf einem ESP32 und zur Einbindung in **Home Assistant** (inklusive Leistungs- und Energiedashboard-Vorbereitung).

> **Hinweis:** Dies ist primär die Dokumentation und Konfiguration meines eigenen Setups. Nutzung auf eigene Verantwortung – Messwerte, Stromrichtungen und Energieberechnungen im eigenen System bitte vorab prüfen.

## Was ausgelesen und berechnet wird

Über die `BCLive`-Characteristic (`4b616912-40bd-428b-bf06-698e5e422cd9`) werden die 18-Byte-Frames ausgewertet:

* **Frame `0x00` (Live-Messwerte & Leistung):**
  * Batteriespannung (`V`)
  * Solarstrom (`A`) und berechnete Solarleistung (`W` = `V × A`)
  * Batteriestrom (`A`, vorzeichenbehaftet aus 3 Bytes)
  * Batterie-Ladeleistung (`W`) und Batterie-Entladeleistung (`W`)
  * Land-/Motor-Ladeleistung (`W`, abgeleitet aus positiver Stromdifferenz `Batteriestrom - Solarstrom`)
* **Frame `0x02` (Battery Computer 1):**
  * Batterieladung (`Ah`) und Ladezustand `SoC` (`%`)
  * Tages-Min-/Max-Werte für Strom (`A`), Ladung (`Ah`) und Spannung (`V`)
* **Frame `0x03` (Battery Computer 2):**
  * Batterietemperatur (`°C`) sowie Tages-Min-/Max-Temperatur (`°C`)
  * Heutige Gesamtlade- und -entlademenge (`Ah`)
* **Info-Characteristic (`c3fbc9e2-676b-9fb5-3749-2f471dcf07b2`):**
  * Firmware-/Gerätekennung als Textsensor

## Einbindung in Home Assistant & Energiedashboard

1. Die YAML-Datei in ESPHome übernehmen, eigene Werte in `secrets.yaml` hinterlegen (`wifi_ssid`, `wifi_password`, `secondary_wifi_ssid`, `secondary_wifi_password`, `fallback_password`, `api_encryption_key`) und die MAC-Adresse des BlueBattery D1 an dein Gerät anpassen.
2. Für die Energieanzeige im Home-Assistant-Energiedashboard (`Wh` bzw. `kWh`) über **Einstellungen -> Geräte & Dienste -> Helfer -> Sensor für Riemann-Summenintegrale** jeweils einen Helfer auf die gewünschten Leistungssensoren (`W`) anlegen (z. B. *BlueBattery Ladeleistung*, *BlueBattery Entladeleistung*, *BlueBattery Land/Motor Ladeleistung*) und im Energiedashboard zuweisen.

## Referenz

Das Byte-Layout der Frames (`0x00`, `0x02`, `0x03`) sowie Skalierungsfaktoren wurden anhand des Python-Projekts (https://github.com/danielfett/bluebattery.py abgeglichen und eigenständig in ESPHome-Lambdas umgesetzt.
