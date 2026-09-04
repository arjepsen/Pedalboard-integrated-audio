# Design Notes

Last updated: 2026-09-03

## Audio architecture

- Selected codec: TAC5212IRGER, mounted directly on the mainboard.
- Primary use: guitar effects and NAM playback using Open Pedalboard or PiPedal software.
- Input 1 is a high-impedance guitar input.
- Input 2 is an optional balanced line input.
- Both codec inputs are differential and AC-coupled.
- The CM connection uses I2S/PCM audio signals plus I2C control.

The TAC5212 combines the required stereo ADC and DAC. Its differential AC-coupled mode offers its best input performance, while programmable input impedance and full-scale settings allow the two analog paths to be adapted in software.

## Guitar input

The first OPA1656 half is a unity-gain buffer. The second produces an equal, inverted signal for the differential ADC input. The target is approximately 2 V peak at the guitar jack with overall analog gain near 1x.

| Part | Purpose and reason |
|---|---|
| C56: 100 nF film, MKS2 footprint | Main input coupling capacitor; film construction suits the high-impedance signal node |
| C64: 100 nF C0G, 1206, DNP | Optional SMD alternative to C56; fit only one of the two capacitors |
| R33: 1 MΩ | Defines normal guitar input impedance and biases the buffer at 4 V |
| R34/R37: 1 kΩ | Equal values set the second amplifier to an inverting gain of -1 |
| R38/R39: 470 Ω and R40: 1 kΩ | Isolate the op-amp outputs and form a balanced divider that gives approximately 1x overall differential gain |
| C57/C58: 4.7 µF Rubycon MF | Low-voltage-coefficient coupling into TAC5212 input 1 |
| D3: H5VUD5BB, DNP | Protection placeholder; not fitted until its capacitance and behavior are accepted |

C56 and R33 form a high-pass corner of about 1.6 Hz. The high-impedance node after C56 should be kept short and away from digital and switching signals.

The OPA1656 was chosen for its FET inputs, very low input bias current, low noise and distortion, and ability to operate from the 10 V single supply.

## Line input

J22 is a balanced TRS line input. A TS plug remains usable because the ring is grounded by the plug.

| Part | Purpose and reason |
|---|---|
| R41/R42: 100 kΩ | Give both jack signal contacts a defined ground reference, provide balanced loading, and discharge the coupling capacitors |
| C62/C63: 4.7 µF Rubycon MF | AC-couple both balanced legs with a corner safely below the audio band |
| R43/R44: 7.5 kΩ | Provide equal series impedance, fault-current limiting, and line-level attenuation before the codec |

With the TAC5212 set to 5 kΩ input impedance, each 7.5 kΩ resistor gives approximately 0.4x voltage transfer. This corresponds to about 5 V RMS differential at J22 for the codec's 2 V RMS differential full scale. The 4.7 µF capacitors also require the TAC5212 input-capacitor quick-charge timing to be configured for more than the 1 µF default.

The former JP4 connection from the jack switch to CM GPIO27 has been removed. The software does not use this automatic stereo-detection signal, and GPIO27 is intentionally unconnected.

## TAC5212 configuration assumptions

- Differential, AC-coupled input mode for both ADC channels.
- Approximately 5 kΩ per input pin unless later testing supports another setting.
- Default 2 V RMS differential full scale for present headroom estimates.
- Use the low-common-mode-variation setting for best noise performance when the finished circuit permits it.
- Configure longer input-capacitor quick-charge timing for the 4.7 µF coupling capacitors.
- Final clocks, startup sequence, register values, and Linux support still require verification.

## Audio outputs

- Use the TAC5212 in four-channel single-ended output mode.
- Two outputs provide a dedicated stereo headphone connection on a 3.5 mm TRS jack.
- Two outputs provide left and right unbalanced line/amp signals on the existing 6.35 mm jacks.
- Configure the headphone pins for headphone drive and the 6.35 mm pins for line drive.
- Route the same stereo program to both output pairs using the TAC5212 playback mixer.
- Retain normal TS-cable compatibility; balanced outputs are not a present requirement.

This arrangement matches the primary desk-practice use without adding a separate headphone-amplifier IC. The single-ended line outputs give lower specified dynamic range than differential mode, but remain well above the expected system requirement. Coupling capacitors, filtering, ESD protection, jack detection, and independent volume/mute behavior remain to be designed.

## Controller

- The original design uses an RP2040, external QSPI flash, crystal, and supporting parts.
- The intended replacement is an STM32G0B1 to reduce the controller subsystem.
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
- Use 0603 passives where practical.
- Fine-pitch and exposed-pad packages require suitable footprints, stencil design, and inspection.

## Important project state

- `audio.kicad_sch` and `psu.kicad_sch` contain the current custom work.
- The guitar and line-input signal paths are present in the schematic.
- The TAC5212 support, digital, and output connections are not complete.
- The PCB has not yet been updated for the integrated audio circuit.
- The remaining mainboard sheets still match the original reference design.
