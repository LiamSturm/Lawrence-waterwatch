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
| 1 | Generic BNC pH Sensor Module + Probe | Replaces DFRobot pH sensor | [Link](https://www.amazon.com/dp/B07KDPQGYD) | $31.30 | Ordered — not arrived |
| 1 | Gikfun Turbidity + DS18B20 Bundle | Replaces DFRobot turbidity and temperature | [Link](https://www.amazon.com/dp/B0FM85VRN4) | $32.88 | Gikfun Turbidity in hand, DS18B20 ordered - not arrived |
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
| 1 | Heltec WiFi LoRa 32 V3 (915MHz) | ESP32-S3 brain with built-in LoRa radio and OLED screen | [Link](https://www.amazon.com/dp/B0D1H1FN9Y) | $34.99 | To Order |
| 1 | KeeYees Logic Level Shifter 4-Ch (10-pack) | Converts 5V sensor signals to 3.3V | [Link](https://www.amazon.com/dp/B07LG646VS) | $7.69 | To Order |
| | **Total** | | | **$42.68** | |

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

This section will be updated after May 2nd with what I actually 
learned and how it changes the project.

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
