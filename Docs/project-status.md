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
| PCB layers | Confirmed | Keep the mainboard at two layers |
| Controller firmware | Confirmed | Port required behavior to C/C++ |

## Work completed so far

- Selected the TAC5212 audio codec.
- Started a TAC5212-based audio schematic.
- Added an OPA1656-based analog input section.
- Added a 4 V analog bias supply.
- Added C64 as a DNP 100 nF C0G/1206 alternative to the through-hole input capacitor.

## Current design state

- The audio changes exist in the schematic only.
- The PCB still matches the original mainboard and still contains the plug-in sound-card footprint.
- The MIDI/controller sheet still contains the original RP2040 subsystem.
- The second TAC5212 input is not yet clearly documented in the schematic as the line input.
- The intended C56 value is 220 nF, but the live schematic still shows 100 nF.
- TAC5212 Linux/driver support and its final register configuration remain open software tasks.

## Next topic

Review and connect the second, line-level TAC5212 input:

- Decide whether its main connection is balanced TRS or unbalanced TS.
- Reuse the useful parts of the original differential line-input network.
- Check input level, impedance, protection, coupling, and RF filtering against TAC5212.
