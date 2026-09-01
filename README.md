# Lawrence WaterWatch

A citizen science initiative to monitor water quality on Lawrence, Kansas waterways.

## What is this?

This project designs, builds, and rigorously validates low-cost IoT sensors to measure pH, temperature, turbidity, and TDS in Lawrence's rivers.

## Why?

Neither the Wakarusa River nor the Kansas River (Kaw) has real-time public water quality monitoring, despite both running straight through Lawrence and carrying agricultural and industrial runoff. That gap is the problem this project solves.

The personal connection: I grew up in Lawrence and watched my mom work at the city's wastewater treatment plant. In 8th grade I designed a component for a water research study she was involved in. That experience taught me that water carries information about a community's health — and right now Lawrence's rivers are speaking a language nobody is listening to.

## What's in this repo

- **[UPDATES.md](https://github.com/LiamSturm/Lawrence-waterwatch/blob/main/UPDATES.md)** — the full project log, numbered and dated, documenting what's been built, what's broken, and why
- **[HARDWARE.md](https://github.com/LiamSturm/Lawrence-waterwatch/blob/main/HARDWARE.md)** — full technical breakdown: wiring, pin assignments, calibration methods, and source code for all four sensors

## Project Status

Phase 1 — All four sensors (temperature, TDS, turbidity, pH) are 
integrated on a Heltec WiFi LoRa 32 V3 node and field-tested at 
Mutt Run on the Wakarusa River. Data transmits over WiFi to a 
self-hosted dashboard, viewable on a phone or laptop on the same 
network — not a public deployment.

Public river deployment (gateway, LoRa network, multi-node buildout) 
was deliberately dropped. Permanent installation on either river 
requires a long-lead-time USACE permit that isn't viable within 
this project's timeline, and an alternative dock-mounting option 
at the KU boathouse was declined. Rather than keep pursuing that 
path, the project redirected toward building the most accurate, 
rigorous instrument possible — see 
[Update 017](https://github.com/LiamSturm/Lawrence-waterwatch/blob/main/UPDATES.md#update-017--september-1-2026) 
for the full reasoning.

**Current phase:** calibrating and validating all four sensors 
against known reference standards, and fixing two known hardware 
issues — a temperature sensor wiring fault and turbidity's 
sensitivity to ambient sunlight.

## What I'm measuring

- pH
- Water temperature
- Turbidity (water clarity)
- TDS

## Built by

Liam Sturm — Lawrence High School, Class of 2027
