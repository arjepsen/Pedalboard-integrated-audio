# Decisions

Last updated: 2026-09-03

## Confirmed

| Decision | Reason |
|---|---|
| Integrate the sound card on the mainboard | Remove the separate sound-card header and reduce the number of boards |
| Use the TAC5212 | Combines a high-performance stereo ADC and DAC, supports differential inputs and outputs, and provides programmable analog settings in one codec |
| Use input 1 for guitar | A normal passive guitar needs a much higher input impedance than the codec provides directly |
| Use input 2 as a balanced line input | Adds a useful optional input without another active buffer stage |
| Use differential, AC-coupled codec inputs | This is the TAC5212 configuration intended for best dynamic-range performance |
| Use one clean 10 V analog rail | A negative rail adds complexity without a demonstrated audio benefit |
| Bias the OPA1656 at 4 V | Provides suitable input and output headroom from the 10 V single supply |
| Use OPA1656 for the guitar front end | Its FET inputs suit a 1 MΩ guitar input, while its low noise and distortion suit audio; one half buffers and the other creates the inverted signal |
| Use 100 nF for C56 | With R33 at 1 MΩ, the input high-pass corner is about 1.6 Hz, safely below the audio band |
| Keep C64 as a DNP C0G alternative to C56 | Provides an SMD assembly option without fitting two capacitors in parallel |
| Use Rubycon 16MF475KB23225 for C57/C58 and C62/C63 | The 4.7 µF non-polar polymer capacitors have low voltage dependence and suit low-distortion codec coupling |
| Use Basic 1% driver resistors | Premium 0.1% thin-film parts offer no meaningful system benefit here |
| Remove `audio_in_stereo` and JP4 | The original GPIO27 jack-detection idea is unused by the published Open Pedalboard software |
| Use four single-ended TAC5212 outputs | Allows simultaneous stereo headphones and stereo line outputs without another amplifier IC |
| Add a dedicated 3.5 mm stereo headphone output | Headphone practice is a primary use case; the TAC5212 provides a genuine headphone-driver mode |
| Keep two unbalanced 6.35 mm line/amp outputs | Preserves the original practical output use with ordinary TS cables while keeping the circuit simple |
| Replace RP2040 with STM32G0B1 | Reduce the controller subsystem and combine functions where sensible |
| Target C/C++ controller firmware | Port the required behavior rather than continuing the original Rust firmware |
| Target JLCPCB production | Prefer economical assembly choices while allowing justified premium or hand-fitted parts |
| Keep a two-layer mainboard | Maintain the cost and construction approach of the existing project |

## Preferences

| Preference | Guidance |
|---|---|
| Prefer JLCPCB Basic parts | Use them when performance and reliability are effectively unchanged |
| Pay more when justified | Audio quality, reliability, or availability can justify a better part |
| Allow limited manual assembly | Suitable for practical parts that can be fitted with the available microscope, soldering station, and heatbed |
| Keep documentation concise | Record actual design choices and reasoning, not incidental parts that happen to be available |

## Undecided

| Question | Current position |
|---|---|
| Guitar-input protection | D3 is currently DNP; choose protection that does not significantly load or distort the high-impedance input |
| Keep external MIDI connectors and circuitry? | Possibly useful later, but no external MIDI equipment is currently planned |
| Exact STM32G0B1 package and pin assignment | Decide after making a complete controller I/O list |
| Which parts should be hand-fitted? | Decide from JLCPCB availability, assembly cost, and soldering difficulty |
| Final TAC5212 configuration | Confirm input impedance, clocking, startup, and Linux support |
| Output coupling, filtering, protection, and jack detection | Finalize from the TAC5212 recommendations when drawing the output circuits |

## Selected parts

| Use | Part | JLCPCB status when last checked |
|---|---|---|
| 4 V divider | R35: 15 kΩ, UNI-ROYAL 0603WAF1502T5E, C22809 | Basic |
| 4 V divider | R36: 10 kΩ, UNI-ROYAL 0603WAF1002T5E, C25804 | Basic |
| Guitar driver | R34/R37/R40: 1 kΩ, 0603WAF1001T5E, C21190 | Basic |
| Guitar driver | R38/R39: 470 Ω, 0603WAF4700T5E, C23179 | Basic |
| Codec coupling | C57/C58/C62/C63: Rubycon 16MF475KB23225, C50394238 | Extended; stock must be rechecked |

Basic/Extended classification and stock are time-dependent and must be checked again before ordering.

## Working rule

The assistant must not change design or project files without explicit approval. The normal workflow is discussion and instructions, followed by changes made by the project owner. Documentation-only edits also require approval.
