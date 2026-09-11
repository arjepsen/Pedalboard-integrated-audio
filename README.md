# Pedalboard Integrated Audio

Modernized Open Pedalboard-derived mainboard for low-latency guitar processing and NAM playback.

## Current design

- Raspberry Pi Compute Module 5 only
- TAC5212 stereo codec integrated on the mainboard
- High-impedance OPA1656 guitar input plus balanced line input
- Dedicated stereo headphone output plus two unbalanced line/amp outputs
- STM32G0B1CET6 controller in C/C++
- Two Waveshare 2inch 240×320 IPS ST7789VW displays
- Native CM5 USB connections for service/recovery, STM32 control, and external USB-A
- Designed to support both Open Pedalboard software and PiPedal where practical
- Target: JLCPCB production on a two-layer mainboard

Audio quality is the first priority, followed by latency, hardware robustness, software compatibility, and convenience/cost.

## Project documentation

- [`Docs/project-status.md`](Docs/project-status.md) — current state and next work
- [`Docs/decisions.md`](Docs/decisions.md) — confirmed design decisions and selected parts
- [`Docs/design-notes.md`](Docs/design-notes.md) — circuit and architecture details
- [`Docs/work-log.md`](Docs/work-log.md) — chronological design record

## Status

The schematic is being redesigned around CM5, integrated TAC5212 audio, STM32 control, and the new display/power architecture. The PCB still needs to be reworked for the new schematic before manufacturing.

## Origin and license

This project is derived from the Open Pedalboard hardware project and retains its CERN Open Hardware Licence Version 2 - Permissive licensing. See [`LICENSE.txt`](LICENSE.txt).
