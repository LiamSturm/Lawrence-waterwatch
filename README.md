# Lawrence WaterWatch

A citizen science project monitoring water quality on Lawrence, Kansas waterways.

## What is this?

This project designs, builds, and tests low-cost IoT sensors that measure pH, temperature, turbidity, and TDS in Lawrence's rivers.

## Why?

Neither the Wakarusa River nor the Kansas River (Kaw) has real-time public water quality monitoring, despite both running straight through Lawrence and carrying agricultural and industrial runoff. That gap is the problem this project solves.

The personal connection: I grew up in Lawrence and watched my mom work at the city's wastewater treatment plant. In 8th grade I designed a component for a water research study she was involved in. That's where I got interested in what water can tell you about a place — and nobody in Lawrence is currently measuring it.

## What's in this repo

* [UPDATES.md](UPDATES.md) — the full project log, numbered and dated, documenting what's been built, what's broken, and why
* [HARDWARE.md](HARDWARE.md) — full technical breakdown: wiring, pin assignments, calibration methods, and source code for all four sensors
* [REFLECTION.md](REFLECTION.md) — project retrospective: what changed, what I learned, what it cost, and what's still untested

## Project Status

Phase 1 complete. All four sensors (temperature, TDS, turbidity, pH) are integrated on a Heltec WiFi LoRa 32 V3 node and field-tested at Mutt Run on the Wakarusa River — outdoors, in direct sunlight, in real river water. Readings were cross-checked against independent tools and line up with published USGS and KDHE data for Kansas waterways. Data transmits over WiFi to a self-hosted dashboard, viewable on a phone or laptop on the same network — not a public deployment.

Public river deployment (gateway, LoRa network, multi-node buildout) was dropped. Permanent installation on either river requires a USACE permit with a lead time this project can't work around, and an alternative dock-mounting option at the KU boathouse was declined. Instead of continuing down that path, the project shifted toward building the most accurate instrument possible — see [Update 017](UPDATES.md#update-017--september-1-2026) for the reasoning and [REFLECTION.md](REFLECTION.md) for the full retrospective.

## What I'm measuring

* pH
* Water temperature
* Turbidity (water clarity)
* TDS

## Built by

Liam Sturm — Lawrence High School, Class of 2027
