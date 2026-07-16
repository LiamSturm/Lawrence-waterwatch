# Project Updates

A running log of what's happening with Lawrence WaterWatch — 
what's been built, what's changed, and why. Updated as the 
project develops.

---

## Update 001 — April 19, 2026

### What happened over the past three weeks

On March 30, 2026 I placed the first orders for Lawrence WaterWatch. 
Here's exactly what was ordered and where things stand:

| Quantity | Item | Source | Link | Price | Status |
|---|------|--------|------|-------|--------|
| 1 | ELEGOO Arduino Uno R3 | Amazon | [Link](https://www.amazon.com/ELEGOO-Board-ATmega328P-ATMEGA16U2-Compliant/dp/B01EWOE0UU) | $14.99 | In Hand |
| 1 | DFRobot pH Sensor V2 (SEN0161-V2) | DFRobot | [Link](https://www.dfrobot.com/product-1782.html) | $39.50 | In Hand - arrived April 20 |
| 1 | DFRobot Turbidity Sensor (SEN0189) | DFRobot | [Link](https://www.dfrobot.com/product-1394.html) | $9.90 | In Hand - arrived April 20 |
| 1 | DFRobot DS18B20 Temperature Kit (KIT0021) | DFRobot | [Link](https://www.dfrobot.com/product-1354.html) | $7.50 | In Hand - arrived April 20 |
| 1 | HiLetgo SD Card Module | Amazon | [Link](https://www.amazon.com/HiLetgo-Adater-Interface-Conversion-Arduino/dp/B07BJ2P6X6) | $6.99 | In Hand |
| 1 | ELEGOO Jumper Wires 120pc | Amazon | [Link](https://www.amazon.com/Elegoo-EL-CP-004-Multicolored-Breadboard-arduino/dp/B01EV70C78) | $6.98 | In Hand |
| 1 | ELEGOO 830 Point Breadboard 3-pack | Amazon | [Link](https://www.amazon.com/dp/B01EV6LJ7G) | $8.99 | In Hand |
| 1 | pH Buffer Calibration Solution 4-Pack | Amazon | [Link](https://www.amazon.com/dp/B01EBFZU7W) | $33.00 | In Hand |
| 1 | Power Bank 30000mAh (YD-818P) | Already Owned | — | $0.00 | In Hand |
| — | Shipping | Amazon & DFRobot | — | $20.00 | — |
| | **Total** | | | **$147.85** | |

### Why the first prototype wasn't built in a week

In the first Instagram reel (April 9) I said the prototype 
would be built in about a week. That didn't happen. Here's 
exactly why.

Three sensors ordered from DFRobot on March 30 — pH, turbidity, 
and temperature — still haven't arrived as of April 19. The 
tracking number provided doesn't work. Three weeks with no 
hardware means no build.

But the delay exposed a more fundamental problem I hadn't caught 
yet: **the original architecture was wrong.**

The Arduino Uno + SD card setup I had ordered would log data 
locally to a chip. You'd have to physically retrieve the SD card 
from the riverbank to read the data. That's not a real-time 
public dashboard — that's a data logger. It doesn't serve the 
community goal this project was built around.

Sitting with the shipping delay forced me to actually work 
through the full system design, and that's when I caught it. 
So the delay, while frustrating, produced a better project.

---

### The new architecture

The immediate goal is straightforward: get water quality data 
into some form of electronic output. The first prototype will 
use the Arduino Uno already in hand to confirm that all four 
sensors are reading accurately.

From there, the next step is getting that data to my phone 
wirelessly via LoRa — cutting the physical retrieval problem 
entirely and proving the wireless link works before the full 
network is deployed.

The final plan is to get the sensors operating as follows:

**Sensor nodes** — Each node measures water quality and 
wirelessly transmits data via LoRa radio.

**Gateway** — A central LoRaWAN gateway receives transmissions 
from all sensor nodes and pushes data to the cloud over WiFi 
or ethernet. One gateway serves the entire network of 5+ nodes.

**Public dashboard** — Data flows from the gateway to a 
publicly accessible real-time dashboard. Anyone in Lawrence 
can see current water conditions without retrieving anything 
from a riverbank.

---

### What's being measured (updated)

| Parameter | Why it matters |
|-----------|----------------|
| pH | Indicates acidification from runoff |
| Water temperature | Affects dissolved oxygen and algal bloom risk |
| Turbidity | Measures water clarity and sediment load |
| TDS (Total Dissolved Solids) | Directly measures nutrient and contaminant load from agricultural runoff |

TDS has been added to the original three measurements. It's 
the most direct indicator of the agricultural runoff problem 
this project was built to monitor.

---

### Order Set #2 — Sensor replacements (ordered April 19)

Switching from DFRobot to Amazon for all sensors. Same 
measurements, Prime shipping, no more waiting on international 
fulfillment with broken tracking. This phase ends with a 
working bench prototype confirming all four sensors read 
accurately.

| Quantity | Item | Purpose | Link | Price | Status |
|---|------|---------|------|-------|--------|
| 1 | Generic BNC pH Sensor Module + Probe | Replaces DFRobot pH sensor | [Link](https://www.amazon.com/dp/B07KDPQGYD) | $31.30 | In Hand |
| 1 | Gikfun Turbidity + DS18B20 Bundle | Replaces DFRobot turbidity and temperature | [Link](https://www.amazon.com/dp/B0FM85VRN4) | $32.88 | In Hand |
| 1 | CQRobot TDS Sensor | New — measures total dissolved solids | [Link](https://www.amazon.com/dp/B08KXRHK7H) | $11.99 | In Hand |
| | **Total** | | | **$76.17** | |

---

### Order Set #3 — Wireless transmission (after prototype is confirmed)

Once all four sensors are confirmed working on the bench, the 
Arduino Uno gets replaced with the Heltec WiFi LoRa 32 V3 — 
an ESP32-S3 board with a built-in LoRa radio. This enables 
each node to wirelessly transmit sensor data miles away to 
a central gateway. The onboard OLED screen displays live 
readings in the field without needing a laptop.

| Quantity | Item | Purpose | Link | Price | Status |
|---|------|---------|------|-------|--------|
| 1 | Heltec WiFi LoRa 32 V3 (915MHz) | ESP32-S3 brain with built-in LoRa radio and OLED screen | [Link](https://www.amazon.com/dp/B0D1H1FN9Y) | $34.99 | In Hand |
| 1 | KeeYees Logic Level Shifter 4-Ch (10-pack) | Converts 5V sensor signals to 3.3V | [Link](https://www.amazon.com/dp/B07LG646VS) | $7.69 | In Hand |
| 1 | 4.7kΩ Resistors 1/4W (Pack of 100) | Pull-up resistor for DS18B20 temperature sensor data line | [Link](https://www.amazon.com/Resistor-10K-AXIAL-Pack-4-7K/dp/B003U42LIC) | $6.00 | In Hand |
| | **Total** | | | **$48.68** | |

---

### Order Set #4 — Gateway and public dashboard (final phase)

Once a single node is transmitting wirelessly, the final step 
is setting up the gateway that receives all node transmissions 
and pushes data to a public dashboard. One gateway serves the 
entire deployed network.

| Quantity | Item | Purpose | Link | Price | Status |
|---|------|---------|------|-------|--------|
| 1 | Dragino LPS8v2 LoRaWAN Gateway (915MHz) | Receives all sensor node transmissions, pushes to cloud | [Link](https://www.amazon.com/dp/B0CQQX2WFL) | TBD | To Order |
| | **Total** | | | **TBD** | |

Gateway placement is still being determined. The goal is to 
position it centrally enough to receive signals from nodes 
on both the Kansas River (Kaw) and the Wakarusa River.

---

### A note on this project's scope

The original README mentioned only the Wakarusa River. 
The project now covers both the **Wakarusa River** and the 
**Kansas River (Kaw)**. Lawrence sits between both. Both 
carry agricultural and industrial runoff. Both deserve 
monitoring.

---

Follow along on Instagram at [@lawrencewaterwatch](https://www.instagram.com/lawrencewaterwatch)

---

## Update 002 — April 20, 2026

### Kansas Data Science Conference — May 2, 2026

On May 2nd I'll be attending the 
[Kansas Data Science Conference 2026 (KDSC26)](https://people.cs.ksu.edu/~safia/KDSC26/) 
at Kansas State University in Manhattan, KS.

KDSC is a statewide event bringing together students, faculty, 
researchers, and industry professionals around data science, AI, 
and community impact. This year's theme — *"From community data 
to community impact"* — is exactly what Lawrence WaterWatch is 
trying to do.

### Why I'm going

The biggest unsolved problem in this project right now is the 
public dashboard. Sensors can collect data. A gateway can push 
it to the cloud. But getting that data into a form that's 
actually useful and accessible to Lawrence residents — a live, 
readable, public interface — is a data problem as much as a 
hardware problem.

KDSC's tracks include Applied Data Science in Agriculture, 
Community Impact, and AI/ML. I'm going specifically to learn 
from people who have already solved the problem of turning 
raw environmental sensor data into something a community can 
actually use.

### What I want to come back with

- A clearer picture of what dashboard platform fits this project
- Any contacts working on environmental or agricultural data 
  problems in Kansas who might be relevant to WaterWatch
- A better understanding of how to structure the data pipeline 
  from sensor to public display

### After the conference

The conference was worth the trip. A few specific takeaways:

**Dashboard platform** — didn't land on a specific platform yet, 
but got a much better sense of what the options look like in 
practice. That decision is coming as the hardware gets closer 
to deployment.

**A useful connection** — met Will Duncan, a data professional 
who offered to help if questions come up as the project develops. 
Having someone to reach out to when the data pipeline gets 
complex is valuable.

**Existing water data sources** — there are organizations and 
agencies that maintain water quality datasets for Kansas 
waterways. Lawrence still has no real-time public monitoring 
at recreational sites, which confirms the gap this project 
is filling. As WaterWatch scales, integrating historical and 
regional data from these sources alongside live sensor readings 
could make the dashboard significantly more useful — giving 
context to what the sensors are measuring in real time.

**What changes:** The scope of the dashboard is now clearer. 
It's not just a live readout of sensor values — the most 
useful version combines real-time sensor data with broader 
regional water quality context. That's a longer term goal 
but worth designing toward from the start.

---

## Update 003 — April 22, 2026

### First prototype build — what happened and what's next

Yesterday was the first hands-on build session for Lawrence WaterWatch. 
All three Order Set #1 sensors were wired up and tested for the 
first time. Here's exactly what happened.

### What worked

**Turbidity sensor** — wired and reading. Confirmed it responds 
to changes in water clarity — NTU values changed visibly when 
turbidity was introduced. Readings are not yet calibrated 
accurately but the sensor is functional.

### What didn't work yet

**pH sensor** — would not complete two-point calibration. The 
probe needs additional conditioning time before the DFRobot 
library will accept calibration inputs. Will retry after 
extended soak in distilled water.

**Temperature sensor (DS18B20)** — returned -127°C, which 
means the Arduino couldn't detect the sensor. Root cause: 
missing 4.7kΩ pull-up resistor on the data line. The adapter 
module does not have it built in as expected. Resistors are 
being ordered.

### What I learned

This was the first time getting hands-on with the full sensor 
suite. Despite the calibration and wiring issues, the session 
was valuable — I now understand exactly how each sensor 
connects, what each one needs to work correctly, and what 
the failure modes look like. That knowledge makes the next 
session faster.

Building something and having it not work is still building. 
The problems are documented. The fixes are clear.

### What's next

- Order 4.7kΩ resistors to fix the temperature sensor
- Continue pH probe conditioning and retry calibration
- Order Set #3 (Heltec WiFi LoRa 32 V3 + logic level shifter) 
  is being ordered now to begin wireless transmission prototyping
- Amazon Order Set #2 sensors still arriving — pH Sensor + DS18B20 bundle will provide additional components to experiment with

---

## Update 004 — June 4, 2026

### Where I've been — and why this repo went quiet

The last substantive update to this repo was May 3rd — the KDSC conference reflection. There was a minor order status update on May 27th, but no real documentation since then. That's about a month of silence, and it deserves a straight explanation.

Late April through mid-May was AP exam season. Studying took over the schedule and WaterWatch had to wait. After exams ended on May 21st, I went straight into an intensive debugging push on the Arduino prototype — working through the sensor issues documented in Update 003. That debugging stretched through the last week and a half of May into early June. I didn't document it in real time because I kept expecting to have something resolved to write about. I didn't.

The repo going quiet wasn't the project going quiet. It was me working through a problem that didn't have a clean answer yet. That's documented now.

### What happened during the debugging push

After Update 003 identified two broken sensors, I spent the following weeks trying to fix them. Here's where each one landed.

**Temperature sensor (DS18B20)** — The 4.7kΩ pull-up resistors from Order Set #3 arrived and were installed correctly. The sensor still returned -127°C. I checked whether the resistor itself was dead and could not confirm either way. Every standard fix in the troubleshooting playbook has been tried. The sensor is not working and the root cause remains unknown.

**pH sensor** — Two-point calibration is a standard process and I followed it correctly. The problem is the probe itself. At this price point the probe quality isn't reliable enough to produce stable readings — values drift significantly during calibration regardless of technique. This is a hardware quality floor, not a calibration error.

**Turbidity sensor** — Still reading and responding to changes in water clarity, but not calibrated to accurate NTU values. This is a known issue and a solvable one — it wasn't the reason for this decision.

### The decision

I'm done debugging this prototype stack. The temperature and pH sensors are not going to be fixed by more time on them — one has an unknown hardware fault, the other is simply not a quality instrument. Continuing to debug them is going around in circles, and this project needs to move forward.

The Arduino Uno prototype served its purpose: it confirmed the build approach, identified what these sensors need to function, and taught me exactly what the failure modes look like. That knowledge carries forward. The hardware doesn't.

### What's next — moving to the Heltec

Almost all of order sets #2 and #3 are in hand:

| Quantity | Item | Purpose | Link | Price | Status |
|---|------|---------|------|-------|--------|
| 1 | Generic BNC pH Sensor Module + Probe | Replaces DFRobot pH sensor | [Link](https://www.amazon.com/dp/B07KDPQGYD) | $31.30 | Ordered — not arrived |
| 1 | Gikfun Turbidity + DS18B20 Bundle | Replacement sensors | [Link](https://www.amazon.com/dp/B0FM85VRN4) | $32.88 | In Hand |
| 1 | CQRobot TDS Sensor | Total dissolved solids | [Link](https://www.amazon.com/dp/B08KXRHK7H) | $11.99 | In Hand |
| 1 | Heltec WiFi LoRa 32 V3 (915MHz) | ESP32-S3 brain with built-in LoRa radio and OLED | [Link](https://www.amazon.com/dp/B0D1H1FN9Y) | $34.99 | In Hand |
| 1 | KeeYees Logic Level Shifter 4-Ch (10-pack) | Converts 5V sensor signals to 3.3V for Heltec | [Link](https://www.amazon.com/dp/B07LG646VS) | $7.69 | In Hand |
| 1 | 4.7kΩ Resistors 1/4W (Pack of 100) | Pull-up resistor for DS18B20 data line | [Link](https://www.amazon.com/Resistor-10K-AXIAL-Pack-4-7K/dp/B003U42LIC) | $6.00 | In Hand |

The immediate priority is wireless transmission — getting the Heltec to successfully send data over LoRa radio before worrying about sensor accuracy. Once the wireless link is confirmed working, sensor calibration follows. Getting the architecture right comes before getting the readings perfect.

---

## Update 005 — June 4, 2026

### Getting a job to fund the project

I started training at Raising Cane's on May 19th and completed 
drive-through training on May 30th. The project has cost $272.70 
so far and that number will keep climbing as hardware deployment 
gets closer. Working at Cane's is how I'll cover it.

---

## Update 006 — June 17, 2026

### Wireless testing delayed — waiting on data cable

Started attempting to test the Heltec WiFi LoRa 32 V3 and 
discovered the USB-C to USB-A data cable I owned was 
non-functional. Can't upload code to the board without it.

I'm fetching a replacement cable from Best Buy today.

Wireless transmission testing will resume once the cable is 
confirmed working.

---

## Update 007 — June 24, 2026

### Heltec WiFi LoRa 32 V3 connected to WiFi

Got the Heltec board recognized by the Windows PC, uploaded 
the first sketch, and confirmed it connects to WiFi. Unplugged 
it from the computer, powered it from a wall outlet alone, and 
it connected to the router independently. The board works.

Next is getting the sensors wired to the Heltec and testing 
wireless data transmission.

---

## Update 008 — June 30, 2026

### First wireless sensor integration — temperature reading live

The DS18B20 temperature sensor is now wired to the Heltec, 
reading water temperature accurately, and transmitting data 
wirelessly to a browser that updates every two seconds. This 
is the first sensor successfully integrated on the Heltec and 
the first sensor to transmit data wirelessly.

Verification: dunked the sensor in the same glass of water as 
a kitchen thermometer. Both read 77°F and are accurate to the nearest whole degree.

This is a breakthrough. The Arduino prototype never got here. 
The DS18B20 returned -127°C on the Arduino and was abandoned. 
The replacement unit from Order Set #2 works perfectly on the 
Heltec.

The delay between Update 007 and now was soldering. The Heltec 
came unsoldered. I borrowed a soldering gun and solder from a 
friend, soldered all accessible pins on both header rows so I 
wouldn't have to return to this later, and got the sensor wired 
in. That took time but it's done.

Next: integrate the remaining three sensors (pH, turbidity, 
TDS) and get all four reading and transmitting together.

---

## Update 009 — July 1, 2026

### Two-sensor wireless node — temperature and TDS live

The CQRobot TDS sensor is now wired to the Heltec and reading 
total dissolved solids. Combined with the DS18B20 from Update 
008, the board is now running a two-sensor wireless node — both 
readings served to a browser over WiFi, updating every two 
seconds, on wall power alone.

**TDS wiring:** GPIO4 through a voltage divider (two 4.7kΩ 
resistors in series between 3.3V and GND, signal read from the 
midpoint). Temperature compensation was added to the TDS 
calculation using the live DS18B20 reading — the formula adjusts 
for how far water temperature is from 25°C, the sensor's 
calibration standard, before applying the conversion to ppm.

**Accuracy status:** The sensor is confirmed functional and 
responding correctly. Tap water read ~370 ppm. Adding salt caused 
a clear jump to ~890 ppm — the sensor is detecting dissolved 
solids proportionally. Full accuracy calibration against a known 
reference solution is still pending. The planned check is 1g of NaCl dissolved in 1L of distilled water, which should read ~1000 ppm.

**What's next:** Distilled water calibration check for TDS, 
then pH and turbidity sensors added to the same node. Final 
goal is all four sensors reading and transmitting simultaneously.

---

## Update 010 — July 2, 2026

### Turbidity sensor integrated — three-sensor node live

The Gikfun TS-300B turbidity sensor is now wired to the Heltec 
alongside the DS18B20 and CQRobot TDS sensor. All three are 
reading simultaneously and displaying on a single auto-refreshing 
WiFi dashboard.

**Wiring:** Getting the sensor reading required diagnosing the 
probe cable's wire-to-pin mapping — the initial wiring left the 
IR emitter unpowered, producing a flat 0 across all conditions. 
Resolved by cross-referencing the datasheet and a comparable 
DFRobot module, then confirming the correct mapping through 
trial: yellow→3, blue→2, red→1.

**Voltage:** Tested VCC at both 5V and 3.3V. At 5V the analog 
output clipped at the ESP32-S3 ADC's ceiling in clear water — 
an overvoltage risk for the ADC pin. Switched to 3.3V, which 
produced a clean dynamic range across conditions.

**Accuracy:** Confirmed the expected inverse relationship — more 
suspended particles means less light reaching the receiver, 
which means lower voltage output. Clear tap water read ~2200 
raw (~1.78V). Heavily turbid water with cocoa powder dropped 
to near 0. The sensor is responding correctly across the full 
range.

---

## Update 011 — July 3, 2026

### pH sensor integrated — all four sensors live

The pH sensor is now wired to the Heltec and reading accurately. 
All four sensors — temperature, TDS, turbidity, and pH — are 
displaying simultaneously on the combined WiFi dashboard.

**GPIO conflict:** Initial wiring used GPIO1, which produced a 
stuck reading immediately. Root cause: GPIO1 is internally 
hard-wired to the Heltec's onboard battery voltage divider and 
can't be used for external sensors. Moved to GPIO6.

**Probe pinning:** After moving to GPIO6, any probe connection 
immediately pinned the ADC to max output (4095/3.3V) regardless 
of solution. Isolated the cause systematically — GPIO6 and the 
bare board both tested fine independently, but any probe attached 
pinned high. Root cause: the board's op-amp circuit is designed 
for 5V and can't center its output correctly at 3.3V. Fixed by 
moving board power to the Heltec's 5V pin and adding a 
two-resistor voltage divider on the signal line to scale the 
output back into the ESP32's 3.3V ADC range.

**Calibration:** Offset calibration by shorting the probe 
connector and adjusting the onboard trim potentiometer to center 
the baseline, then a full two-point calibration using pH 7.0 
and pH 4.0 buffer solutions, averaging 15+ readings per buffer 
with simultaneous temperature readings. Nernst-equation 
temperature compensation added to the pH calculation, scaling 
the calibrated slope based on current water temperature relative 
to the 24°C calibration temperature.

**Accuracy:** pH 7 buffer reads 6.98–7.02. pH 4 buffer reads 
3.97–4.01. Readings in tap and distilled water are unstable — 
a known characteristic of glass pH electrodes in weakly-buffered 
liquids, not a setup fault.

### What's next

- Verify TDS and turbidity accuracy against known reference solutions
- Post a formal hardware breakdown documenting how the full four-sensor node functions
- Field test the node in actual river water at the deployment sites
- Begin the next phase — extending transmission range and building out the sensor network

---

## Update 012 — July 13, 2026

### TDS sensor — root cause found and fixed

TDS readings had drifted to match tap water almost exactly 
(~370–448 ppm) even in distilled water, which should read near 
0. Ruled out electrode residue by cleaning with vinegar — no 
change. Confirmed the real problem with a controlled test: the 
sensor gave identical readings in both air and distilled water, 
meaning it wasn't responding to the liquid at all.

**Root cause:** A wiring error introduced during other sensor 
rewiring that day. The TDS module's power and signal lines were 
unintentionally sharing a resistor network meant only for 
scaling output signal, rather than getting clean, direct power. 
The ESP32 was reading a fixed voltage off the resistor divider 
itself — not the sensor's actual output. That's why the reading 
never changed regardless of what was in the water.

**Fix:** Rewired the module with direct, unobstructed 
connections — straight to 3.3V, straight to GND, straight to 
the ESP32 signal pin, no resistors in the path. This module's 
output is already 3.3V-compatible, so no divider is needed.

**Verified:** Distilled water now reads ~0 ppm. Tap water reads 
a real, distinct value (~190–370 ppm depending on test). 
Concentrated salt water read ~1900+ ppm. The sensor responds 
correctly and proportionally across a wide range.

---

## Update 013 — July 15, 2026

### Turbidity sensor — accuracy validated with dilution series

Ran a controlled dilution series to confirm the turbidity sensor 
gives a real, usable response across a range of conditions — not 
just "clear" versus "very murky."

**Method:** Switched from cocoa powder to cornstarch as the test 
medium. Cocoa absorbs light rather than scattering it, while 
cornstarch scatters light the way real suspended river sediment 
does — a closer optical match. Ran a 9-point series in 1 cup of 
distilled water: baseline, then 1/8, 1/4, 3/8, 1/2, 5/8, 3/4, 
7/8, and 1 full teaspoon of cornstarch, averaging 15+ readings 
at each concentration.

**Results:**

| Cornstarch | Avg Raw ADC Value |
|---|---|
| 0 tsp (baseline) | ~2354 |
| 1/8 tsp | ~1680 |
| 1/4 tsp | ~1136 |
| 3/8 tsp | ~811 |
| 1/2 tsp | ~625 |
| 5/8 tsp | ~479 |
| 3/4 tsp | ~381 |
| 7/8 tsp | ~311 |
| 1 tsp | ~263 |

The response was fully monotonic — voltage dropped consistently 
as concentration increased, with no flat spots or reversals 
across the entire range. The curve shape also matched known 
turbidity sensor physics: large drops at low concentrations, 
progressively smaller drops at higher concentrations, since the 
relationship between suspended particles and light blockage 
compresses at high turbidity.

No wiring or code changes were needed. The existing setup, 
already integrated into the combined dashboard, produces 
accurate, trustworthy readings.

---
