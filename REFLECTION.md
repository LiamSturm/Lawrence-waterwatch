# Project Reflection

I started this project in March 2026 wanting to build 
infrastructure — five-plus sensor nodes across the Kaw and 
Wakarusa, talking to a shared gateway, feeding a public dashboard 
anyone in Lawrence could check. Six months in, that's not what 
exists. What exists is a single, rigorously calibrated instrument 
that's been validated in the field. This is the story of why, and 
what I'd tell someone starting the same project today.

The wall wasn't technical. It was jurisdictional — permanent 
sensor placement on a navigable river requires a federal permit 
with a lead time longer than I have before college, and the one 
alternative I found (mounting on the KU boathouse dock) was 
declined. Rather than keep pushing on a door that wasn't going to 
open in time, I redirected the project toward the part I could 
actually finish: proving the sensor itself is accurate.

### What I went in thinking
A public monitoring network — multiple nodes, one gateway, live 
data anyone in Lawrence could pull up.

### What changed
Permitting turned out to be the real bottleneck, not hardware. The 
U.S. Army Corps of Engineers' permit timeline doesn't fit a high 
schooler's runway, the boathouse option closed, and the City of 
Lawrence conversation is still open but not something I'm driving. 
I redirected toward building the most accurate instrument I could, 
instead of chasing deployment.

### What I learned
- Soldering, breadboarding, and prototyping from a bare board — 
  going from raw components off Amazon to a functioning sensor
- Full integration and calibration across four different sensor 
  types (pH, temperature, turbidity, TDS)
- How to actually apply what I'd learned in environmental science, 
  AP Chem, and math to a live system, not just a worksheet
- How to document and organize a technical project for public 
  consumption — structuring a README, a hardware breakdown, and a 
  running build log so a stranger could follow the work and 
  replicate the instrument
- How to communicate technical work publicly — documented the 
  build on Instagram (@lawrencewaterwatch) as a running log, which 
  is how a Lawrence Times reporter found the project and covered it
- How permitting and jurisdiction actually work for a public 
  project — and how much that can shape what's realistic
- What it takes to self-fund something real — I took a job at 
  Raising Cane's specifically to bankroll this, on top of 
  everything technical

### What's still untested
End-to-end LoRa transmission — the gateway is bought and still 
unconfigured. And everything real field deployment requires at 
scale: weatherproof enclosures, permanent mounting, solar power. 
None of that got built, because none of it mattered until 
placement was possible.

### What I'd do differently
**Check jurisdiction and permitting before ordering hardware.** I 
had sensors in hand before I understood that permanent placement 
on a navigable waterway is a federal question. That sequencing 
cost real weeks.

**Design the full system before buying parts.** My first 
architecture was an Arduino Uno logging to an SD card — $147.85 
spent before I realized that retrieving a chip from a riverbank 
isn't real-time public data. It's a data logger, not a monitoring 
network. Working through the complete data path first would have 
caught that on paper instead of on my desk.

**Buy from a vendor you can actually chase.** Three sensors 
ordered from DFRobot took three weeks to arrive with a tracking 
number that never resolved. I eventually rebought the same 
measurements from Amazon. Lead time and recourse matter as much 
as unit price when you're one person on a deadline.

### Cost: actual vs. the build that didn't happen
Actual spend: $442.69, across four order sets of sensors, 
microcontrollers, calibration supplies, and the LoRaWAN gateway.

A full five-node build — two nodes on the Wakarusa, three on the 
Kaw, each with its own complete sensor set, plus two gateways to 
cover both rivers — would have run $1,048.70:

| Qty | Item | Reason | Unit | Line Total |
|---|---|---|---|---|
| 3 | ELEGOO Jumper Wires 120pc | 3 boxes to cover 5 sensors | $6.98 | $20.94 |
| 2 | ELEGOO 830 Breadboard 3-pack | 6 breadboards — 5 sensors plus 1 spare | $8.99 | $17.98 |
| 2 | pH Buffer Calibration 4-Pack | 2 bottles of 4pH/7pH to calibrate 5 probes | $33.00 | $66.00 |
| 4 | Power Bank 30000mAh | Had 1 already; 4 more for 5 nodes | $10.50 | $42.00 |
| 5 | BNC pH Sensor + Probe | 1 per node | $31.30 | $156.50 |
| 5 | Gikfun Turbidity + DS18B20 Bundle | Yields 5 turbidity, 25 temp — overshoot, but soldering damages some | $32.88 | $164.40 |
| 5 | CQRobot TDS Sensor | 1 per node | $11.99 | $59.95 |
| 5 | Heltec WiFi LoRa 32 V3 | 1 board per node | $34.99 | $174.95 |
| 1 | 4.7kΩ Resistors (100pk) | 1 pack covers all nodes | $6.00 | $6.00 |
| 2 | SenseCAP M2 Gateway | 2 needed — 2 nodes Wakarusa, 3 Kaw | $169.99 | $339.98 |
| | **Total** | | | **$1,048.70** |

More than double actual spend, for a deployment that was never 
going to clear permitting in time anyway.

### Where this leaves it
The instrument works — calibrated, field-validated twice, and 
proven to hold up under the exact conditions that broke it the 
first time around. If the City of Lawrence ever clears a path for 
public placement, the multi-node build is still the plan, not 
abandoned. Until then, this is what "done" looks like: anyone can 
read this repo, follow the wiring and calibration steps, and build 
a sensor that reads real water quality data to their phone.
