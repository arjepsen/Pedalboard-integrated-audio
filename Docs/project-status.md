# Project Status

Last updated: 2026-09-03

## Goal

Adapt the Open Pedalboard mainboard into a practical, integrated guitar-effects and NAM player that can use Open Pedalboard and PiPedal software and is suitable for JLCPCB production.

## Current direction

| Area | Status | Direction |
|---|---|---|
| Audio codec | Confirmed | Mount a TAC5212 directly on the mainboard |
| Guitar input | Circuit complete | High-impedance OPA1656 buffer and differential ADC drive; final input-protection choice remains open |
| Line input | Circuit complete | Balanced TRS, AC-coupled into TAC5212 input 2 |
| Codec integration | In progress | Complete power, decoupling, control, digital-audio, and output connections |
| Audio outputs | Direction confirmed | Dedicated 3.5 mm stereo headphones plus two unbalanced 6.35 mm line/amp outputs |
| Controller | Planned | Replace the RP2040 subsystem with an STM32G0B1 |
| External MIDI | Undecided | Preserve the option if it does not add unreasonable cost or complexity |
| Manufacturing | Confirmed | Target JLCPCB assembly and retain the two-layer board |

## Completed schematic work

- Selected the TAC5212 stereo codec.
- Added an OPA1656-based guitar input with a 4 V bias supply.
- Set C56 to the 100 nF design value.
- Added C64 as a DNP 100 nF C0G/1206 alternative footprint for C56.
- Completed the balanced line input using C62/C63, R41/R42, and R43/R44.
- Removed JP4 and the unused `audio_in_stereo` detection net; CM GPIO27 is intentionally unconnected.

## Current project state

- The custom audio work exists in the schematic only.
- The PCB still represents the original mainboard and plug-in sound card.
- The TAC5212 analog inputs are connected, but its remaining support and output circuits are unfinished.
- The output arrangement is decided, but its coupling, filtering, protection, and jack wiring have not been drawn.
- D3 is DNP while the guitar-input protection choice is reviewed.
- The MIDI/controller sheet still contains the original RP2040 subsystem.
- TAC5212 Linux support and final register configuration remain open software tasks.

## Next topic

Complete the TAC5212 core connections, starting with its supplies, grounding, internal-regulator capacitor, reference capacitor, and local decoupling. Then review the I2C/I2S connections and output circuit.
