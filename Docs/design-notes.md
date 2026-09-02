# Design Notes

Last updated: 2026-09-02

## Audio

- Selected codec: TAC5212IRGER.
- The codec will be mounted directly on the mainboard.
- Input 1 is a Hi-Z input for normal passive guitar pickups.
- The first OPA1656 half is a unity-gain buffer; the second creates an equal, inverted signal for differential ADC input.
- Design target: approximately 2 V peak at the guitar jack with overall analog gain near 1×.
- Input 2 will remain a line input.
- A 4 V analog bias rail has been introduced.
- The CM4 connection uses I2S/PCM audio signals plus a control bus.

### Guitar input values

| Part | Purpose |
|---|---|
| C56: 220 nF WIMA MKS2 | Main DC-blocking capacitor; hand-fitted |
| C64: 100 nF C0G, 1206, DNP | SMD alternative to C56 |
| R33: 1 MΩ | Guitar input impedance and bias |
| R34/R37/R40: 1 kΩ | Differential-driver network |
| R38/R39: 470 Ω | Output isolation |
| C57/C58: 4.7 µF Rubycon PMLCAP | Low-distortion coupling into TAC5212 |

The high-impedance node after C56 should be short and kept away from digital and switching signals.

### TAC5212 assumptions

- Guitar channel: differential, AC-coupled input mode.
- Intended high-performance input setting: approximately 5 kΩ per input pin.
- Differential full scale used for the headroom estimate: 2 V RMS.
- Final register settings, clocks, reset/startup, and Linux support still require verification.

## Controller

- The original design uses an RP2040, external QSPI flash, crystal, and supporting parts.
- The intended replacement is an STM32G0B1.
- The replacement must be based on the required controls and connections, not only on chip cost.
- STM32G0B1KET6 in LQFP-32 is the current leading candidate, but is not frozen.
- Prove a simultaneous pin assignment for USB, debug, MIDI, expression inputs, I2C, switches, encoders, and LEDs before selecting the package.
- Port the required controller behavior from the original Rust code to C/C++.

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
- Use 0603 passives where practical.

## Important project state

- `audio.kicad_sch` and `psu.kicad_sch` contain the current custom work.
- The PCB has not yet been updated for the integrated audio circuit.
- The remaining mainboard sheets still match the original reference design.
- The live C56 value currently reads 100 nF and must eventually be reconciled with the confirmed 220 nF choice.
- D3, the guitar-input TVS device, is currently DNP; final low-capacitance protection remains to be reviewed.
