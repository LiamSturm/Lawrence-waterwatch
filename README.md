# Lawrence WaterWatch

A citizen science initiative to monitor water quality on Lawrence, Kansas waterways.

## What is this?

This project designs, fabricates, and deploys low-cost IoT sensors to measure pH, temperature, turbidity, and TDS in Lawrence's rivers — and makes that data publicly accessible to the Lawrence community.

## Why?

Neither the Wakarusa River nor the Kansas River (Kaw) has real-time public water quality monitoring, despite both running straight through Lawrence and carrying agricultural and industrial runoff. That gap is the problem this project solves.

The personal connection: I grew up in Lawrence and watched my mom work at the city's wastewater treatment plant. In 8th grade I designed a component for a water research study she was involved in. That experience taught me that water carries information about a community's health — and right now Lawrence's rivers are speaking a language nobody is listening to.

## What's in this repo

- **[UPDATES.md](https://github.com/LiamSturm/Lawrence-waterwatch/blob/main/UPDATES.md)** — the full project log, numbered and dated, documenting what's been built, what's broken, and why
- **[HARDWARE.md](https://github.com/LiamSturm/Lawrence-waterwatch/blob/main/HARDWARE.md)** — full technical breakdown: wiring, pin assignments, calibration methods, and source code for all four sensors

## Project Status

Phase 1 — All four sensors (temperature, TDS, turbidity, pH) are integrated on a Heltec WiFi LoRa 32 V3 node, transmitting live over WiFi to a self-hosted dashboard. First field deployment completed July 2026 at Mutt Run on the Wakarusa River.

**Next:** verifying TDS and turbidity accuracy against known reference solutions, then extending transmission range and building out a multi-node sensor network across both rivers.

## What I'm measuring

- pH
- Water temperature
- Turbidity (water clarity)
- TDS

## Built by

Liam Sturm — Lawrence High School, Class of 2027
