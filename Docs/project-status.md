# Project Status

Last updated: 2026-09-11

## Goal

Adapt the Open Pedalboard mainboard into a practical, CM5-only integrated guitar-effects and NAM player that can use Open Pedalboard and PiPedal software and is suitable for JLCPCB production.

## Current direction

| Area | Status | Direction |
|---|---|---|
| Audio codec | Confirmed | Mount a TAC5212 directly on the mainboard |
| Guitar input | Circuit complete | High-impedance OPA1656 buffer and differential ADC drive; final input-protection choice remains open |
| Line input | Circuit complete | Balanced TRS, AC-coupled into TAC5212 input 2 |
| Codec integration | Schematic substantially complete | 48 kHz stereo I2S, CM5 clock producer, TAC5212 clock consumer, ultra-low-latency filters, 25 ms input-cap quick charge; final analog input settings and Linux configuration remain |
| Audio outputs | Circuit drawn | Dedicated 3.5 mm stereo headphones plus two unbalanced 6.35 mm line/amp outputs |
| Power supply | Circuit updated | 12 V input, 5.08 V / 6 A buck, separate 3.3 V rails, and quiet 10 V analog rail; TPS7A2033 retained for the general 3.3 V rail |
| Controller | Schematic replaced | STM32G0B1CET6 with two Waveshare 2inch LCD Modules, direct CM5 USB, SWD, controls, and optional MIDI |
| Compute module | Confirmed | The redesigned board supports CM5 only |
| Connectivity | Schematic replaced | CM5 native USB pairs directly serve service USB-C, internal STM32, and external USB-A; the former hub and mux are removed |
| External MIDI | Schematic drawn, initially DNP | DIN MIDI footprints, interface circuit, STM32 pins, and software direction are retained for future fitting |
| Manufacturing | Confirmed | Target JLCPCB assembly and retain the two-layer board |

## Completed schematic work

- Selected the TAC5212 stereo codec.
- Added an OPA1656-based guitar input with a 4 V bias supply.
- Set C56 to the 100 nF design value.
- Added C64 as a DNP 100 nF C0G/1206 alternative footprint for C56.
- Completed the balanced line input using C62/C63, R41/R42, and R43/R44.
- Removed JP4 and the unused `audio_in_stereo` detection net; CM GPIO27 is intentionally unconnected.
- Added TAC5212 power, local decoupling, I2C/I2S connections, headphone outputs, and line/amp outputs.
- Added C79 10 µF on TAC5212 DREG in parallel with C65 100 nF, matching the local DREG decoupling requirement.
- Selected 48 kHz stereo I2S with 32-bit slots, CM5 as clock producer, TAC5212 as clock consumer, and no separate MCLK oscillator.
- Selected TAC5212 ultra-low-latency ADC/DAC filters for the primary guitar path.
- Selected 25 ms input-capacitor quick charge for the 4.7 µF codec input coupling capacitors.
- Reworked the main power tree around TPS56637RPAR, AP22653W6-7, TPS7A2033, and LT3045.
- Selected STM32G0B1CET6 in LQFP-48 for the controller redesign.
- Replaced the original display choice with two Waveshare 2inch LCD Modules, SKU 17344, using ST7789VW over shared SPI.
- Replaced the display connections with JST PH 8-pin mainboard headers, J15 left and J17 right, with separate chip selects.
- Removed redundant display-connector capacitors; the Waveshare modules provide local 1 µF decoupling and C24 remains 10 µF at the digital 3.3 V regulator output.
- Rechecked the general 3.3 V rail: the two selected displays contribute about 92 mA maximum combined, and the TPS7A2033 has comfortable current and thermal margin for the expected total load.
- Confirmed that the redesigned mainboard is CM5-only and will use native CM5 USB ports rather than preserving the CM4 hub/mux architecture.
- Confirmed that the STM32 controller will preserve the original bidirectional USB-MIDI behaviour and optional DIN MIDI routing.
- Replaced the RP2040, external QSPI flash, crystal, and RP2040 support circuit with STM32G0B1CET6.
- Selected crystal-less STM32 operation: HSI48 with USB SOF clock recovery for USB and the internal clock for the remaining controller functions.
- Added filtered expression inputs, a five-pin SWD header, debug-UART test points, reset/boot buttons, and a status LED.
- Confirmed that all optional external-MIDI connectors and avoidable interface parts are marked DNP for the initial build.
- Retained the AT24CS01 EEPROM because the controller software uses it for frequently updated runtime state.

## Current project state

- The custom audio work exists in the schematic only.
- The PCB still represents the original mainboard and plug-in sound card.
- The integrated audio circuitry is substantially drawn but still needs final review, ERC cleanup, and PCB implementation.
- D3 is DNP while the guitar-input protection choice is reviewed.
- The controller sheet now contains the STM32 subsystem and no longer contains the RP2040 or external QSPI flash as active circuitry.
- J15 and J17 are the left/right Waveshare display connectors using JST PH 8-pin headers.
- The CM5 symbol and footprint are present as CM2, and the former USB hub and mux have been removed.
- CM5 native USB pairs now serve service USB-C, the internal STM32, and external USB-A directly.
- The hierarchical `RGB_DATA` connection now links STM32 PA8 to the LEDs sheet.
- The general 3.3 V rail has been checked and the existing TPS7A2033 plus C24 = 10 µF are retained.
- Rail capacitance and startup/inrush must continue to be checked as local bulk capacitors are added or changed.
- A project-wide value and sourcing-field audit remains open. Use consistent passive values and populate manufacturer, manufacturer part, and JLCPCB part fields where they are useful.
- TAC5212 clocking/startup direction is now substantially settled; exact ADC input impedance/full-scale/gain settings and final Linux register configuration remain open.
- STM32 migration will also require a new USB identity, updated Pedalboard OS matching, and USB-DFU firmware-update support instead of RP2040 UF2 flashing.

## Next sequence

1. Finalize TAC5212 ADC input impedance/full-scale/gain settings against the guitar and line-input resistor networks.
2. Perform a project-wide component-value and metadata audit before generating the manufacturing BOM.
3. Complete final USB protection, service-port, ERC, and power-startup/inrush review.
4. Rework the PCB for the integrated audio, CM5, STM32, Waveshare display connectors, and updated power circuits.
