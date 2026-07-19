# Lawrence WaterWatch — Hardware Breakdown

A technical reference for how the four-sensor node functions: wiring, 
pin assignments, and calibration methods for each sensor, plus how 
they're combined into a single WiFi dashboard.

**Board:** Heltec WiFi LoRa 32 V3 (ESP32-S3)

---

## Pin Assignments

| Sensor | Signal Pin | Power | Ground | Additional Wiring |
|---|---|---|---|---|
| DS18B20 (Temperature) | GPIO5 | 3.3V | GND | 4.7kΩ pull-up resistor between + and data lines |
| CQRobot TDS | GPIO4 | 3.3V | GND | Direct wiring, no resistors |
| Gikfun TS-300B (Turbidity) | GPIO2 | 3.3V | GND | Direct wiring, no resistors |
| pH Sensor (PH-4502C-style) | GPIO6 | 5V | GND | Two 4.7kΩ resistors in series (Po → GND) forming a voltage divider, GPIO6 taps the midpoint |

**Note:** GPIO1 is internally wired to the Heltec's onboard battery voltage divider and should never be assigned to an external sensor.

---

## DS18B20 — Temperature

**Wiring:** Digital sensor using the OneWire protocol. Data line requires 
a 4.7kΩ pull-up resistor bridging the power and data lines — without it, 
the sensor returns an explicit "not found" error rather than a bad reading.

**Calibration:** None required — reports temperature directly via the 
DallasTemperature library (`getTempCByIndex(0)`), no ADC voltage 
conversion involved.

**Validated against:** Physical kitchen thermometer, matched at 77°F 
during bench testing.

**Confirmed accuracy:** Field test (Wakarusa River) read 82.2°F 
submerged, consistent across five readings, after settling from ~90°F 
open-air ambient temperature.

---

## CQRobot TDS

**Wiring:** Direct connection — no resistors or voltage divider. Native 
analog output already falls within the ESP32's 0–3.3V ADC range.

**Calibration:**

Temperature compensation (using live DS18B20 reading):

compensationCoefficient = 1.0 + 0.02 × (tempC − 25.0)
compensatedVoltage = rawVoltage / compensationCoefficient

Voltage-to-ppm conversion (standard polynomial, valid 0–1000 ppm range):

tdsValue = (133.42 × V³ − 255.86 × V² + 857.39 × V) × 0.5

**Confirmed accuracy:**

| Test condition | Reading |
|---|---|
| Distilled water | ~0 ppm |
| Lawrence tap water | 147–370 ppm |
| Concentrated salt water | ~1900+ ppm (correct direction/proportion; exceeds formula's reliable range) |
| Field (Wakarusa River) | 147–158 ppm, consistent across 5 readings |

**Pending:** Formal calibration check with known NaCl concentration 
(1g NaCl / 1L distilled water, expected ~1000 ppm).

---

## Gikfun TS-300B — Turbidity

**Wiring:** Direct connection — no resistors or voltage divider. Probe 
connects via 3-wire cable through a JST-style connector to a driver 
board.

| Probe wire | Driver board pin |
|---|---|
| Yellow | 3 |
| Blue | 2 |
| Red | 1 |
| (unused) | 4 |

Driver board G/A/D/V header: G→GND, A→GPIO2 (signal), D→unconnected, V→3.3V.

**Calibration:** No formalized NTU standard (e.g. formazin) yet — 
pending item. Validated instead via a 9-point cornstarch dilution 
series in distilled water (cornstarch chosen over cocoa powder — it 
scatters light like real sediment rather than absorbing it).

| Cornstarch added (1 cup water) | Avg Raw ADC Value |
|---|---|
| 0 (baseline) | ~2354 |
| 1/8 tsp | ~1680 |
| 1/4 tsp | ~1136 |
| 3/8 tsp | ~811 |
| 1/2 tsp | ~625 |
| 5/8 tsp | ~479 |
| 3/4 tsp | ~381 |
| 7/8 tsp | ~311 |
| 1 tsp | ~263 |

Response was fully monotonic with a diminishing-returns curve shape — 
consistent with Beer-Lambert-type light attenuation at higher particle 
concentrations. No temperature compensation applied — turbidity is an 
optical property, not electrochemical.

**Known field limitation:** Field readings (raw 3643–3863) came in 
higher than the indoor baseline (~2354), due to ambient sunlight 
interference — a documented characteristic of this sensor class. 
Sunlight's infrared content isn't distinguishable from the sensor's 
own emitter signal, inflating readings outdoors. Field readings aren't 
yet directly comparable to the indoor calibration curve. Planned fix: 
opaque shield around the optical tip to block ambient light.

---

## pH Sensor (PH-4502C-style, Amazon B07KDPQGYD)

**Wiring:** Requires 5V power — the op-amp circuit can't center its 
output correctly at 3.3V, causing the signal to pin at max ADC value 
the instant a probe is connected. A two-resistor (4.7kΩ each) voltage 
divider on the signal line scales the native 0–5V output down to a 
safe 0–2.5V range for the ESP32's 3.3V ADC.

Board header pins used: Po (signal, through divider) → GPIO6, G → GND, 
V+ → 5V. To (external temp probe) and Do (digital threshold) left 
unconnected — temperature compensation is handled in software via the 
DS18B20 instead.

**Calibration:**

1. **Offset calibration** (one-time, hardware): probe disconnected, 
   BNC center pin shorted to outer barrel, onboard trim potentiometer 
   adjusted until post-divider voltage at GPIO6 stabilized at 1.250V.

2. **Two-point calibration** using pH 7.00 and pH 4.00 buffers, 
   15–20 readings averaged per buffer with simultaneous temperature:

   | Buffer | Voltage | Temperature |
   |---|---|---|
   | pH 7.00 | 1.2436V | 23.94°C |
   | pH 4.00 | 1.5096V | 24.32°C |

   Derived constants:

   slope = (7.0 − 4.0) / (v7 − v4) = −11.28
   offset = 7.0 − (slope × v7) = 21.03
   T_CAL = 24.13°C

3. **Nernst equation temperature compensation** (applied at read-time):

   compensatedSlope = phSlope × (currentTempC + 273.15) / (T_CAL + 273.15)
   pH = compensatedSlope × currentVoltage + phOffset

**Confirmed accuracy:** pH 7 buffer reads 6.98–7.03. pH 4 buffer reads 
3.97–4.04. Both held steady after full integration into the WiFi 
dashboard code.

**Known limitation:** Unreliable, physically implausible readings in 
weakly-buffered water — distilled, tap, and river water all produced 
erratic or falsely alkaline readings (e.g. 10.3–10.8 in the Wakarusa 
River), despite correct, consistent buffer calibration. This is a 
documented limitation of single-junction glass pH electrodes: the 
concentrated internal reference electrolyte leaches out abnormally 
fast in low-conductivity samples, creating an unstable diffusion 
potential. Not a defect in this setup's wiring or calibration.

Planned fix, two-phase:
- **Short-term:** extend probe soak/stabilization time before logging 
  field readings, cross-check periodically against a colorimetric pH 
  test kit
- **Long-term:** after the network/gateway phase, replace with a 
  low-ionic-strength or double-junction reference electrode built for 
  environmental water sampling

---

## Combined WiFi Dashboard

All four sensors are read within a single HTTP handler on an ESP32 
`WebServer` instance (port 80):

- Temperature is read first each cycle, since its value feeds both the 
  TDS temperature compensation and the pH Nernst compensation
- TDS and turbidity are read via direct `analogRead()` calls, converted 
  to voltage using the ESP32-S3's 12-bit ADC scale
- pH is read via a dedicated function that averages 20 ADC samples to 
  reduce signal noise, then passed through temperature compensation
- All four values are rendered on a single auto-refreshing HTML page 
  (2-second interval), styled as individual cards
- UTF-8 character encoding is explicitly declared to fix a prior bug 
  where the °F/°C symbol rendered incorrectly
- The board runs an mDNS responder (`waterwatch.local`), so the 
  dashboard is reachable without hardcoding an IP — confirmed working 
  over both home WiFi and a phone hotspot in the field

---

## Full Source Code

```cpp
#include <WiFi.h>
#include <WebServer.h>
#include <ESPmDNS.h>
#include <OneWire.h>
#include <DallasTemperature.h>

const char* ssid     = "YOUR_WIFI_SSID";
const char* password = "YOUR_WIFI_PASSWORD";

#define ONE_WIRE_BUS 5
#define TDS_PIN 4
#define TURBIDITY_PIN 2
#define PH_PIN 6

OneWire oneWire(ONE_WIRE_BUS);
DallasTemperature sensors(&oneWire);
WebServer server(80);

float phSlope  = -11.28;
float phOffset = 21.03;
float T_CAL    = 24.13;  // pH calibration temperature, °C

float readPHVoltage() {
  long sum = 0;
  for (int i = 0; i < 20; i++) { sum += analogRead(PH_PIN); delay(10); }
  float adc = sum / 20.0;
  return adc * (3.3 / 4095.0);
}

float readPH(float tempC) {
  float compensatedSlope = phSlope * (tempC + 273.15) / (T_CAL + 273.15);
  return compensatedSlope * readPHVoltage() + phOffset;
}

void handleRoot() {
  sensors.requestTemperatures();
  float tempC = sensors.getTempCByIndex(0);
  float tempF = (tempC * 9.0 / 5.0) + 32.0;

  int rawTDS = analogRead(TDS_PIN);
  float voltage = rawTDS * (3.3 / 4095.0);
  float compensationCoeff = 1.0 + 0.02 * (tempC - 25.0);
  float compensatedVoltage = voltage / compensationCoeff;
  float tdsValue = (133.42 * compensatedVoltage * compensatedVoltage * compensatedVoltage
                  - 255.86 * compensatedVoltage * compensatedVoltage
                  + 857.39 * compensatedVoltage) * 0.5;

  int rawTurbidity = analogRead(TURBIDITY_PIN);
  float turbidityVoltage = rawTurbidity * (3.3 / 4095.0);

  float phVoltage = readPHVoltage();
  float phValue = readPH(tempC);

  String html = "<html><head>";
  html += "<meta charset='UTF-8'>";
  html += "<meta http-equiv='refresh' content='2'>";
  html += "<style>";
  html += "body{font-family:sans-serif;text-align:center;margin-top:60px;background:#f5f5f5;}";
  html += "h1{font-size:42px;color:#2196F3;}";
  html += ".card{background:white;border-radius:12px;padding:30px;margin:20px auto;width:300px;box-shadow:0 2px 8px rgba(0,0,0,0.1);}";
  html += ".label{font-size:16px;color:#888;margin-bottom:8px;}";
  html += ".value{font-size:42px;font-weight:bold;color:#333;}";
  html += ".unit{font-size:18px;color:#888;}";
  html += ".sub{font-size:14px;color:#aaa;margin-top:4px;}";
  html += ".footer{font-size:12px;color:#bbb;margin-top:30px;}";
  html += "</style></head><body>";
  html += "<h1>Lawrence WaterWatch</h1>";

  html += "<div class='card'>";
  html += "<div class='label'>Temperature</div>";
  html += "<div class='value'>" + String(tempF, 1) + "<span class='unit'> °F</span></div>";
  html += "<div class='sub'>" + String(tempC, 2) + " °C</div>";
  html += "</div>";

  html += "<div class='card'>";
  html += "<div class='label'>Total Dissolved Solids</div>";
  html += "<div class='value'>" + String(tdsValue, 0) + "<span class='unit'> ppm</span></div>";
  html += "<div class='sub'>Temperature compensated</div>";
  html += "</div>";

  html += "<div class='card'>";
  html += "<div class='label'>Turbidity</div>";
  html += "<div class='value'>" + String(turbidityVoltage, 2) + "<span class='unit'> V</span></div>";
  html += "<div class='sub'>Raw: " + String(rawTurbidity) + "</div>";
  html += "</div>";

  html += "<div class='card'>";
  html += "<div class='label'>pH</div>";
  html += "<div class='value'>" + String(phValue, 2) + "</div>";
  html += "<div class='sub'>" + String(phVoltage, 3) + " V, temp compensated</div>";
  html += "</div>";

  html += "<div class='footer'>Updates every 2 seconds</div>";
  html += "</body></html>";

  server.send(200, "text/html", html);
}

void setup() {
  Serial.begin(115200);
  delay(1000);
  sensors.begin();

  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);
  Serial.print("Connecting to WiFi");
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("");
  Serial.println("WiFi connected!");
  Serial.print("IP address: ");
  Serial.println(WiFi.localIP());

  if (MDNS.begin("waterwatch")) {
    Serial.println("mDNS responder started: http://waterwatch.local");
  } else {
    Serial.println("mDNS failed to start - use the IP address above instead");
  }

  server.on("/", handleRoot);
  server.begin();
}

void loop() {
  server.handleClient();
}
```

*Wiring diagrams and photos to be added in a future revision.*
