# Work Log

## 2026-09-11 - Waveshare displays and 3.3 V rail confirmed

- Replaced the HS20S010B display choice with two Waveshare 2inch LCD Modules, SKU 17344, using ST7789VW over the existing shared SPI architecture.
- Selected JST PH 8-pin vertical mainboard connectors, J15 for the left display and J17 for the right display.
- Confirmed separate left/right chip-select signals while sharing BL, reset, D/C, SCK, MOSI, power, and ground.
- Removed the redundant display-connector decoupling capacitors because each Waveshare module already includes local 1 µF supply decoupling.
- Rechecked the general 3.3 V rail with the actual display load: about 46 mA maximum per display, about 92 mA combined.
- Retained TPS7A2033PDBVR for the general 3.3 V rail; expected total load remains comfortably below its 300 mA rating with acceptable thermal margin.
- Retained C24 at 10 µF on the digital 3.3 V regulator output for transient margin.
- Added the Waveshare display, module schematic, and ST7789VW datasheets to the repository.

## 2026-09-11 - STM32 controller, TFT, and CM5 USB migration drawn

- Replaced the RP2040, external firmware flash, and crystal with U3, an STM32G0B1CET6.
- Selected crystal-less USB operation using HSI48 and CRS synchronized from the CM5 USB SOF signal.
- Added the controller pin allocation, expression-input filters, SWD/debug connections, and status/boot functions.
- Added two HS20S010B TFT connectors with shared SPI/control and separate chip selects.
- Confirmed from the module information that `BLK` is a logic input; both displays can be driven directly from PA6.
- Replaced the CM4-era USB hub and mux with direct CM5 USB connections for service USB-C, internal STM32, and external USB-A.
- Verified that the optional DIN MIDI group is marked DNP in both the KiCad source and an exported BOM.
- Retained the AT24CS01 EEPROM because the existing controller software writes changing preset, switch, and encoder state to it.
- Standardized the documented passive-value convention on `100n`, `1u`, `4.7u`, `1k5`, and similar forms without redundant unit letters.
- Verified that the hierarchical `RGB_DATA` connection links U3 PA8 to the LED sheet.
- Left the general 3.3 V capacity audit as the next power task.
- Added a later whole-project audit of component values and sourcing fields before PCB/BOM release.

## 2026-09-09 - USB software and MIDI direction reviewed

- Reviewed the maintained Open Pedalboard software and current PiPedal USB behaviour.
- Confirmed that Open Pedalboard should retain bidirectional class-compliant USB-MIDI and its useful controller configuration behaviour when moving to STM32.
- Identified the RP2040-specific USB identity and UF2 updater as software that must change for STM32 USB DFU.
- Confirmed that PiPedal can use the STM32 for incoming MIDI control without modification; dynamic display feedback would require a future companion service.
- Confirmed that optional DIN MIDI input/output will remain in the design but the connectors and avoidable interface parts will initially be DNP.
- Confirmed that the CM4 symbol, footprint, and CM4-specific net names must be replaced with a verified CM5 representation before USB rewiring.
- Selected CM5 representation and direct native USB routing as the next schematic work, before the STM32 replacement.

## 2026-09-09 - CM5-only scope confirmed

- Confirmed that the redesigned mainboard will support CM5 only.
- Selected separate native CM5 USB 2.0 connections for service/flashing, the internal STM32, and external USB-A.
- CM4 compatibility is no longer a design requirement.
- The saved schematic still contains the old hub and mux until the replacement circuit is fully verified.

## 2026-09-09 - Power and controller direction updated

- Reworked the power tree for a regulated 12 V input.
- Selected TPS56637RPAR for approximately 5.08 V / 6 A, with margin for CM5 and external USB loads.
- Retained AP22653W6-7 for current-limited external USB power.
- Selected separate TPS7A2033 regulators for digital 3.3 V and codec analog 3.3 V.
- Retained LT3045 for the quiet 10 V guitar-front-end rail.
- Confirmed STM32G0B1CET6 in LQFP-48 and two HS20S010B/ST7789V2 TFTs as the controller baseline.
- Traced the existing USB hub and mux functions and identified the CM5 native-USB simplification; no connectivity circuit was changed during that audit.

## 2026-09-03 - Output direction selected

- Confirmed that the TAC5212 provides configurable headphone drivers rather than only line-level outputs.
- Selected a dedicated 3.5 mm stereo headphone output as a primary feature.
- Retained two unbalanced 6.35 mm line/amp outputs for normal pedalboard use.
- Chose the TAC5212 four-channel single-ended mode so both output pairs can operate without another amplifier IC.
- Left output coupling, filtering, protection, jack detection, and software configuration for later design.

## 2026-09-03 - Input circuits and documentation reviewed

- Verified the guitar input and balanced line-input paths against the saved schematic.
- Confirmed C56 is designed as 100 nF; incidental substitute values are not design decisions.
- Recorded the component values and concise reasoning for both input paths.
- Traced the original `audio_in_stereo` net to a planned GPIO27 jack-detection function.
- Confirmed the published Open Pedalboard software does not use that signal.
- Removed JP4 and the `audio_in_stereo` labels; CM GPIO27 is intentionally unconnected.
- Identified the TAC5212 core supplies, digital interface, and output circuit as the next unfinished audio block.

## 2026-09-03 - Previous design handover reviewed

- Reconciled the earlier ChatGPT design record with the live KiCad files.
- Recorded the two-layer constraint, 10 V analog rail, 4 V bias, and OPA1656 topology.
- Recorded the 100 nF film design capacitor and Rubycon codec-coupling capacitors.
- Recorded the Basic 1% resistor decision and exact known JLCPCB parts.
- Recorded the C/C++ firmware direction and provisional STM32G0B1KET6 candidate.
- Flagged the DNP guitar-input protection device as an open choice.

## 2026-09-02 - Project orientation

- Identified `PedalBoard-integrated-audio` as the active project.
- Treated the other Open Pedalboard folders as reference material.
- Confirmed that TAC5212 and OPA1656 work is present in the audio schematic.
- Confirmed that the PCB still represents the original plug-in sound-card design.
- Confirmed that the RP2040 controller section has not yet been replaced.
- Recorded the JLCPCB and manual-assembly preferences.
- Created the project documentation structure.

## Logging format

Future entries should contain only:

- Conclusions reached
- Decisions changed
- Work actually completed
- Important open questions
