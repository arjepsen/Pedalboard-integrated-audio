# Project Status

Last updated: 2026-09-02

## Goal

Adapt the Open Pedalboard mainboard for a custom, integrated design that is practical to manufacture through JLCPCB.

## Current direction

| Area | Status | Direction |
|---|---|---|
| Audio codec | Confirmed | Use the TAC5212 on the mainboard |
| Audio input 1 | In progress | Instrument input with an external input buffer |
| Audio input 2 | Confirmed | Keep as a line input |
| Controller | Planned | Replace the RP2040 subsystem with an STM32G0B1 |
| External MIDI | Undecided | Preserve the option if it does not add unreasonable cost or complexity |
| Manufacturing | Confirmed | Target JLCPCB assembly |

## Work completed so far

- Selected the TAC5212 audio codec.
- Started a TAC5212-based audio schematic.
- Added an OPA1656-based analog input section.
- Added a 4 V analog bias supply.

## Current design state

- The audio changes exist in the schematic only.
- The PCB still matches the original mainboard and still contains the plug-in sound-card footprint.
- The MIDI/controller sheet still contains the original RP2040 subsystem.
- The second TAC5212 input is not yet clearly documented in the schematic as the line input.

## Next topic

Review the TAC5212 section before continuing with PCB integration:

- Power and decoupling
- Clocking and CM4 digital-audio connection
- Instrument-input buffer
- Second line input
- Outputs and connector protection

