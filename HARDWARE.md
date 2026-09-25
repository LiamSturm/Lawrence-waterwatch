# Lawrence WaterWatch — Hardware Breakdown

A technical reference for how the four-sensor node functions: wiring, 
pin assignments, calibration methods, and current firmware — as of 
September 24, 2026.

**Board:** Heltec WiFi LoRa 32 V3 (ESP32-S3FN8), CP2102 USB bridge, 
programmed in Arduino IDE 2.

**Power:** USB or a YD-818P 30,000mAh power bank.

**Form factor:** Breadboard prototype.

The node measures temperature, TDS, turbidity (NTU), and pH, and 
streams live readings to an auto-refreshing WiFi dashboard.

---

## Development Note

I designed the system, chose the sensors and wiring, ran every 
calibration and field test, and debugged the hardware. I used AI to 
help write the Arduino code based on the logic and calibration values 
I gave it.

---

## Pin Assignments

| Sensor | Signal Pin | Power | Ground | Additional Wiring |
|---|---|---|---|---|
| DS18B20 (Temperature) | GPIO5 | 3.3V | GND | 4.7kΩ pull-up between + and data |
| CQRobot TDS | GPIO4 | 3.3V | GND | Direct wiring, no resistors |
| Gikfun TS-300B (Turbidity) | GPIO2 | 5V | GND | Two 4.7kΩ resistors in series forming a voltage divider; GPIO2 taps the midpoint |
| pH (PH-4502C-style, Amazon B07KDPQGYD) | GPIO6 | 5V | GND | Two 4.7kΩ resistors in series forming a voltage divider; GPIO6 taps the midpoint |

**GPIO1 is internally wired to the Heltec's onboard battery voltage 
divider.** Never assign it to an external sensor.

Both 5V sensors use the same divider design: the sensor signal runs 
through one 4.7kΩ resistor to a midpoint, then through a second 
4.7kΩ resistor to GND. The midpoint goes to the ESP32 pin. Because 
the two resistors are equal, the pin sees exactly half the sensor's 
voltage, keeping it under the 3.3V ADC limit. Firmware multiplies 
the measured voltage back up where needed.

---

## DS18B20 — Temperature

**Wiring:** Digital OneWire sensor on GPIO5, 3.3V. A 4.7kΩ pull-up 
between power and data is required — without it, the sensor returns 
exactly −127.00°C, an explicit "not found" error rather than a 
garbage value.

**Build note:** The sensor ships with bare, unterminated leads. Short 
male-pin jumper segments were soldered onto all three leads and 
insulated with electrical tape.

**Calibration:** None needed. Reports °C directly via the 
DallasTemperature library (`getTempCByIndex(0)`). Read first every 
cycle, since both TDS and pH compensation depend on it.

**Validation:**

| Test | Reading |
|---|---|
| Kitchen thermometer cross-check | Matched at 77°F |
| Wakarusa River (bench-era test) | 82.2°F, consistent across 5 readings |
| Mutt Run, Sept 7, 2026 | 84.9°F |

**Known behavior:** Occasional intermittent dropouts, which show as 
−127.00°C — not wrong values.

---

## CQRobot TDS

**Wiring:** Direct to GPIO4 at 3.3V. Native output is already within 
ADC range.

**Calibration:**

Temperature compensation using the live DS18B20 reading:
```
compensationCoefficient = 1.0 + 0.02 × (tempC − 25.0)
compensatedVoltage = rawVoltage / compensationCoefficient
```

Voltage-to-ppm conversion (standard polynomial, reliable to ~1000 ppm):
```
tdsValue = (133.42 × V³ − 255.86 × V² + 857.39 × V) × 0.5
```

**Validation:**

| Condition | Reading |
|---|---|
| Distilled water | ~0 ppm |
| Lawrence tap water | 147–370 ppm |
| Concentrated salt water | ~1900 ppm (correct direction; above reliable range) |
| Wakarusa River (bench-era test) | 147–158 ppm |
| Mutt Run, Sept 7, 2026 | ~254 ppm |

---

## Gikfun TS-300B — Turbidity

**Wiring:** Rated 5V DC, 0–4.5V analog output, powered at 5V. Signal 
passes through the two-resistor divider to GPIO2, bringing the max 
~4.5V down to ~2.25V at the pin. Firmware multiplies the measured 
voltage by 2.0 to recover the true sensor voltage. The ESP32-S3 has 
no ADC2/WiFi conflict, so GPIO2 reads correctly with WiFi active.

**Probe cable → driver board:**

| Probe wire | Driver board pin |
|---|---|
| Yellow | 3 |
| Blue | 2 |
| Red | 1 |
| — | 4 (unused) |

Driver board header: G → GND, A → divider top (midpoint → GPIO2), 
D → unconnected, V → 5V.

**Why 5V:** The sensor was originally run at 3.3V. Undervolting 
dimmed the IR emitter and shrank signal headroom against ambient 
light. Running at rated voltage with a divider fixed this.

**Physical specs:** The upper housing is not waterproof — only the 
tip goes underwater, per the manufacturer. There is no removable 
protective cap; the two clear prongs are the finished optical 
hardware.

**Sunlight shield:** Ambient sunlight IR is indistinguishable from 
the sensor's own emitter. Early unshielded field readings were 
inflated (raw 3643–3863). A duct-tape sleeve was tried first — it 
passed indoors but failed outdoors (raw ~2970 vs. ~2290 baseline), 
since duct tape doesn't block enough IR. The current shield is a 
section of gray PVC pipe slipped over the optical tip, extending 
well past the prongs — the walls block ambient light, and the open 
bottom lets water flow through. Outdoor tap water in direct sun now 
reads raw ~2343 / 3.78V, matching the indoor baseline.

**Calibration — two-point linear NTU:**

| Point | Solution | Raw | Voltage |
|---|---|---|---|
| 0 NTU | Clear tap water | 2290 | 3.68V |
| ~29.6 NTU | 3 tsp 2% reduced-fat milk in 5 cups water | ~1377 (9 readings, range 1367–1389) | 2.21V |

NTU for point 2 was derived from a dilution calculation: 14.79 mL 
milk / 1197.74 mL total = 0.01235 dilution ratio, multiplied by 
~2,400 NTU (a commonly cited approximation for undiluted 2% milk, 
not a measured or certified value) → ~29.64 NTU.

```
slope = 29.6 / (2290 − 1377) = 0.03242 NTU per raw count
ntuValue = (2290 − rawTurbidity) × 0.03242, clamped to a minimum of 0
```

Verification: tap water reads 0.3–1.0 NTU. This is a milk-dilution 
approximation, not traceable to a certified formazin/AMCO standard. 
No temperature compensation is applied — turbidity is optical, not 
electrochemical.

**Supporting evidence — cornstarch dilution series:** Taken at the 
earlier 3.3V supply, so these raw values aren't comparable to current 
readings. Demonstrates fully monotonic response with a 
diminishing-returns curve, consistent with Beer-Lambert-type 
attenuation. Cornstarch was chosen because it scatters light like 
real sediment, rather than absorbing it.

| Cornstarch (1 cup water) | Avg Raw ADC |
|---|---|
| 0 | ~2354 |
| 1/8 tsp | ~1680 |
| 1/4 tsp | ~1136 |
| 3/8 tsp | ~811 |
| 1/2 tsp | ~625 |
| 5/8 tsp | ~479 |
| 3/4 tsp | ~381 |
| 7/8 tsp | ~311 |
| 1 tsp | ~263 |

---

## pH Sensor (PH-4502C-style, BNC probe, Amazon B07KDPQGYD)

**Wiring:** Requires 5V — the op-amp can't center its output at 3.3V, 
and the signal pins at max ADC as soon as a probe is connected. Po → 
two-resistor divider → GPIO6 at midpoint (0–5V becomes 0–2.5V). G → 
GND, V+ → 5V. To and Do are left unconnected — temperature 
compensation is done in software via the DS18B20.

**Offset calibration** (one-time, hardware): probe disconnected, BNC 
center pin shorted to the outer barrel, onboard trim pot adjusted 
until GPIO6 read 1.250V.

**Two-point calibration** (15–20 readings averaged per buffer):

| Buffer | Voltage | Temp |
|---|---|---|
| pH 7.00 | 1.2436V | 23.94°C |
| pH 4.00 | 1.5096V | 24.32°C |

Derived constants:
```
slope = (7.0 − 4.0) / (v7 − v4) = −11.28
offset = 7.0 − (slope × v7) = 21.03
T_CAL = 24.13°C
```

**Nernst temperature compensation** at read time:
```
compensatedSlope = phSlope × (tempC + 273.15) / (T_CAL + 273.15)
pH = compensatedSlope × voltage + phOffset
```

pH is averaged over 20 ADC samples per reading.

**Buffer validation:** pH 7 buffer reads 6.98–7.03. pH 4 buffer reads 
3.97–4.04. Both held after integration into the dashboard firmware.

**Field behavior:** Single-junction glass electrodes are unstable in 
low-ionic-strength water — the reference electrolyte leaches 
abnormally fast, creating an unstable diffusion potential. Early 
field readings were falsely alkaline (10.3–10.8 in the Wakarusa 
River, 9.5+ in tap water). Storing the probe in pH 7 buffer between 
uses gives it an extended conditioning soak, which resolved the 
instability — field readings climbed steadily back into a plausible 
range as the probe conditioned:

- 8.56 after a couple of minutes' soak (Lawrence Times shoot)
- Tap water matched an HTH Spa test strip on Sept 5 (pool-formulated 
  strip, rough sanity check only)
- Outdoor tap water 6.86–7.46, then 6.96 (Sept 6)
- Mutt Run, 7.38–7.65 (Sept 7)

**Storage:** The probe is stored in pH 7 buffer. This is the working 
solution — the extended soak it provides is what stabilized the 
electrode in the field.

---

## Combined WiFi Dashboard

A single HTTP handler on an ESP32 `WebServer`, port 80:

- Temperature is read first each cycle, since it feeds both TDS and 
  pH compensation
- TDS and turbidity are read via `analogRead()` using the 12-bit ADC 
  scale (3.3V / 4095)
- Turbidity is converted to true voltage (×2.0), then to NTU
- pH is read via a function averaging 20 samples, then 
  temperature-compensated
- All four values render as cards on an HTML page that auto-refreshes 
  every 2 seconds
- `<meta charset='UTF-8'>` is required — without it the degree symbol 
  renders as "Â°"
- An mDNS responder serves the page at `http://waterwatch.local`, 
  confirmed working over home WiFi and a phone hotspot
- **Gotcha:** iPhone hotspot SSIDs use a curly apostrophe (’) — the 
  firmware string must match exactly

---

## Field Validation — Mutt Run, September 7, 2026

All four sensors together, outdoors in direct sun, in natural water, 
on power bank.

| Sensor | Reading |
|---|---|
| Turbidity | 1.0–1.4 NTU |
| pH | 7.38–7.65 |
| TDS | ~254 ppm |
| Temperature | 84.9°F |

The measurement point sits just below the Clinton Lake dam outflow — 
reservoir settling explains the low turbidity. Mainstem 
Wakarusa/Kansas River sites would be expected to read higher. This 
is the same site where unshielded turbidity readings previously 
failed; these readings are physically plausible and consistent. No 
reference meter was used on site for this test.

---

## Known Limitations

- **Breadboard connections:** jumpers can loosen during transport, 
  occasionally causing all sensor readings to drop out after a 
  power-source switch, while the Heltec stays responsive on WiFi and 
  Serial. Not root-caused. Did not recur during stable operation or 
  the Sept 7 field test.
- **Turbidity calibration** is an approximation, not certified.

---

## Deployment Hardware — Built Only If River Access Is Approved

The sensor node is complete and field-validated. The items below are 
the hardware that would turn a validated instrument into a deployable 
one. None of them have been built, and none will be unless the City 
of Lawrence grants approval to place a node on the river for a period 
of time. If that approval comes through, this is the work that 
follows. Without it, the project is done as it stands.

**Enclosure:**
- LeMotech IP65 ABS junction box, 200×120×75mm, 4× M16 glands
- The pH BNC panel nut can act as a bulkhead pass-through
- Likely needs 1–2 extra glands

**Mounting:**
- Bank-mounted post above the flood line, probes cabled to the water
- Needs a BNC extension for pH and spliced extensions for TDS and 
  turbidity

**Power:**
- Keep the YD-818P bank — its built-in 1W solar panel is trickle only
- Add a separate 5–10W IP67 USB solar panel
- Duty-cycled draw is estimated at ~1–1.5mA, but that requires 
  deep-sleep firmware that doesn't exist yet — current firmware runs 
  WiFi continuously

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
  float turbidityVoltage = rawTurbidity * (3.3 / 4095.0) * 2.0;  // x2 recovers true voltage through divider
  float ntuValue = (2290 - rawTurbidity) * 0.03242;               // two-point NTU calibration
  if (ntuValue < 0) ntuValue = 0;

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
  html += "<div class='value'>" + String(ntuValue, 1) + "<span class='unit'> NTU</span></div>";
  html += "<div class='sub'>" + String(turbidityVoltage, 2) + " V · Raw: " + String(rawTurbidity) + "</div>";
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
