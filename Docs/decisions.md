# Decisions

Last updated: 2026-09-02

## Confirmed

| Decision | Reason |
|---|---|
| Integrate the sound card on the mainboard | Remove the separate header-mounted sound card |
| Use the TAC5212 | New integrated audio solution |
| Use input 2 as a line input | Only input 1 needs the instrument-input treatment |
| Replace RP2040 with STM32G0B1 | Reduce the controller subsystem and combine functions where sensible |
| Target JLCPCB production | Cost and assembly choices should suit JLCPCB |
| Keep a two-layer mainboard | Board cost and the existing project architecture favor two layers |
| Use one clean 10 V analog rail | A negative rail adds complexity without a demonstrated audio benefit |
| Bias the OPA1656 at 4 V | Improves usable headroom on a 10 V single supply |
| Use OPA1656 for the guitar front end | One half buffers the guitar; the other produces the inverted differential signal |
| Fit 220 nF WIMA MKS2 for C56 | User-owned, linear coupling capacitor; hand-fitted at 5 mm pitch |
| Use Rubycon 16MF475KB23225 for C57/C58 | Low-voltage-coefficient 4.7 µF coupling into the TAC5212 |
| Use Basic 1% driver resistors | Premium 0.1% thin-film parts offer no meaningful system benefit here |
| Target C/C++ controller firmware | Port the required behavior rather than continuing the original Rust firmware |

## Preferences

| Preference | Guidance |
|---|---|
| Prefer JLCPCB Basic parts | Use them when performance and reliability are effectively unchanged |
| Pay more when justified | Audio quality, reliability, or availability can justify a better part |
| Allow some manual assembly | Suitable for parts that are practical with a microscope, soldering station, and heatbed |
| Keep documentation concise | Record concrete conclusions without excessive technical detail |

## Undecided

| Question | Current position |
|---|---|
| Keep external MIDI connectors and circuitry? | Possibly useful later, but no external MIDI equipment is currently planned |
| Exact STM32G0B1 package and pin assignment | Decide after making a complete controller I/O list |
| Which parts should be hand-fitted? | Decide from JLCPCB availability, assembly cost, and soldering difficulty |
| Final TAC5212 configuration | Confirm input mode, clocking, startup, and Linux support |
| Final line-input circuit | Check the original balanced input against TAC5212 requirements |
| Final output circuit | Review after the two input paths and codec supplies are settled |

## Selected parts

| Use | Part | JLCPCB status when last checked |
|---|---|---|
| 4 V divider | R35: 15 kΩ, UNI-ROYAL 0603WAF1502T5E, C22809 | Basic |
| 4 V divider | R36: 10 kΩ, UNI-ROYAL 0603WAF1002T5E, C25804 | Basic |
| Guitar driver | R34/R37/R40: 1 kΩ, 0603WAF1001T5E, C21190 | Basic |
| Guitar driver | R38/R39: 470 Ω, 0603WAF4700T5E, C23179 | Basic |
| Codec coupling | C57/C58: Rubycon 16MF475KB23225, C50394238 | Extended; stock must be rechecked |

Basic/Extended classification and stock are time-dependent and must be checked again before ordering.

## Working rule

The assistant must not change design or project files without explicit approval. The normal workflow is discussion and instructions, followed by changes made by the project owner.
