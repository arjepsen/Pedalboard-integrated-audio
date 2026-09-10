# Project Status

Last updated: 2026-09-09

## Goal

Adapt the Open Pedalboard mainboard into a practical, CM5-only integrated guitar-effects and NAM player that can use Open Pedalboard and PiPedal software and is suitable for JLCPCB production.

## Current direction

| Area | Status | Direction |
|---|---|---|
| Audio codec | Confirmed | Mount a TAC5212 directly on the mainboard |
| Guitar input | Circuit complete | High-impedance OPA1656 buffer and differential ADC drive; final input-protection choice remains open |
| Line input | Circuit complete | Balanced TRS, AC-coupled into TAC5212 input 2 |
| Codec integration | Schematic substantially complete | Verify final settings, ERC details, PCB layout, and Linux configuration |
| Audio outputs | Circuit drawn | Dedicated 3.5 mm stereo headphones plus two unbalanced 6.35 mm line/amp outputs |
| Power supply | Circuit updated | 12 V input, 5.08 V / 6 A buck, separate 3.3 V rails, and quiet 10 V analog rail |
| Controller | Decision confirmed | Replace RP2040 with STM32G0B1CET6 and replace the OLEDs with two ST7789V2 TFTs |
| Compute module | Confirmed | The redesigned board supports CM5 only |
| Connectivity | Direction confirmed | Replace the hub/mux arrangement with separate CM5 native USB 2.0 connections |
| External MIDI | Direction confirmed | Retain DIN MIDI footprints and STM32/software support, but initially mark the connector and avoidable interface parts DNP |
| Manufacturing | Confirmed | Target JLCPCB assembly and retain the two-layer board |

## Completed schematic work

- Selected the TAC5212 stereo codec.
- Added an OPA1656-based guitar input with a 4 V bias supply.
- Set C56 to the 100 nF design value.
- Added C64 as a DNP 100 nF C0G/1206 alternative footprint for C56.
- Completed the balanced line input using C62/C63, R41/R42, and R43/R44.
- Removed JP4 and the unused `audio_in_stereo` detection net; CM GPIO27 is intentionally unconnected.
- Added TAC5212 power, local decoupling, I2C/I2S connections, headphone outputs, and line/amp outputs.
- Reworked the main power tree around TPS56637RPAR, AP22653W6-7, TPS7A2033, and LT3045.
- Selected STM32G0B1CET6 in LQFP-48 and two HS20S010B/ST7789V2 TFTs for the controller redesign.
- Confirmed that the redesigned mainboard is CM5-only and will use native CM5 USB ports rather than preserving the CM4 hub/mux architecture.
- Confirmed that the STM32 controller will preserve the original bidirectional USB-MIDI behaviour and optional DIN MIDI routing.
- Confirmed that DIN MIDI hardware will be present as an initially unpopulated option.

## Current project state

- The custom audio work exists in the schematic only.
- The PCB still represents the original mainboard and plug-in sound card.
- The integrated audio circuitry is substantially drawn but still needs final review, ERC cleanup, and PCB implementation.
- D3 is DNP while the guitar-input protection choice is reviewed.
- The MIDI/controller sheet still contains the original RP2040 subsystem.
- The connectivity sheet still contains the USB2514B hub and FSUSB42 mux from the CM4-compatible design.
- CM1 still uses the original CM4 symbol, footprint, and CM4-specific net names; these must be replaced and verified for CM5 before the USB circuit is rewired.
- TAC5212 Linux support and final register configuration remain open software tasks.
- STM32 migration will also require a new USB identity, updated Pedalboard OS matching, and USB-DFU firmware-update support instead of RP2040 UF2 flashing.

## Next sequence

1. Replace the CM4 symbol/footprint and CM4-specific names with a verified CM5 representation.
2. Rebuild USB connectivity around three direct CM5 connections: service USB-C, internal STM32, and external USB-A.
3. Verify USB power switching, role handling, ESD protection, and recovery access.
4. Replace the RP2040 and display subsystem with STM32G0B1CET6 and the two TFTs.
