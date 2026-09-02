# Design Notes

Last updated: 2026-09-02

## Audio

- Selected codec: TAC5212IRGER.
- The codec will be mounted directly on the mainboard.
- Input 1 is intended for an instrument and uses an external buffer.
- The current buffer uses an OPA1656 dual audio op-amp.
- Input 2 will remain a line input.
- A 4 V analog bias rail has been introduced.
- The CM4 connection uses I2S/PCM audio signals plus a control bus.

## Controller

- The original design uses an RP2040, external QSPI flash, crystal, and supporting parts.
- The intended replacement is an STM32G0B1.
- The replacement must be based on the required controls and connections, not only on chip cost.

## MIDI

- External MIDI is not presently required.
- Future MIDI support may still be useful.
- Avoid removing the option until the cost, board space, and STM32 pin requirements are understood.

## Manufacturing

- Primary manufacturer and assembler: JLCPCB.
- Basic parts are preferred when there is little or no disadvantage.
- Extended parts are acceptable when there is a clear benefit.
- Manual fitting is acceptable for a limited number of practical components.
- Fine-pitch or exposed-pad packages need suitable footprints, stencil design, and inspection.

## Important project state

- `audio.kicad_sch` and `psu.kicad_sch` contain the current custom work.
- The PCB has not yet been updated for the integrated audio circuit.
- The remaining mainboard sheets still match the original reference design.

