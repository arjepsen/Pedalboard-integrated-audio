# Decisions

Last updated: 2026-09-09

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
| Replace RP2040 with STM32G0B1CET6, LQFP-48 | The two TFTs make the extra I/O and routing margin preferable to the 32-pin option |
| Use two 2.0-inch ST7789V2 TFTs | Shared SPI with DMA gives ample display performance without full framebuffers |
| Target C/C++ controller firmware | Port the required behavior rather than continuing the original Rust firmware |
| Specify a regulated 12 V input, ±10% | Provides suitable margin for the 10 V analog regulator without unnecessary converter stress |
| Use TPS56637RPAR for the 5 V rail | A modern 6 A synchronous buck provides CM5 and USB-current margin with few external parts |
| Set the main 5 V rail to approximately 5.08 V | Provides wiring and load-transient margin while staying within the CM supply range |
| Keep AP22653W6-7 for external USB power | Provides controlled, current-limited USB VBUS; set to approximately 1.5 A with 15 kΩ |
| Use separate TPS7A2033 regulators for digital 3.3 V and codec analog 3.3 V | Keeps codec analog power isolated without an excessive component count |
| Keep LT3045 for the 10 V analog rail | Its low noise is useful for the guitar front end, and hand assembly is acceptable here |
| Make the redesigned mainboard CM5-only | Current Open Pedalboard software targets CM5, PiPedal supports the Pi 5 platform, and CM5 provides enough native USB ports to simplify the carrier |
| Use CM5 native USB ports instead of the hub/mux arrangement | Dedicate separate USB 2.0 pairs to service/flashing, the internal STM32, and external USB-A; leave SuperSpeed pairs unused |
| Replace CM4-specific symbols and net names with CM5 versions | The redesigned board no longer supports CM4, so its schematic and documentation should describe the actual target rather than retain misleading compatibility names |
| Preserve the Open Pedalboard controller behaviour on STM32 | Keep class-compliant bidirectional USB-MIDI and the useful existing MIDI/configuration protocol while replacing RP2040-specific hardware code |
| Keep optional DIN MIDI hardware, initially DNP | The software already supports DIN-to-USB and USB-to-DIN routing; retaining footprints allows the feature to be added later without paying for unused parts now |
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
| Final flexible STM32 GPIO assignments | Fixed peripheral pins are selected; choose footswitch and encoder pins for easiest routing |
| Which parts should be hand-fitted? | Decide from JLCPCB availability, assembly cost, and soldering difficulty |
| Final TAC5212 configuration | Confirm input impedance, clocking, startup, and Linux support |
| USB protection and service-port details | Finalize ESD parts, role handling, and the external-port power-control connection |
| STM32 USB identity and updating | Select a legitimate VID/PID and replace the RP2040 UF2 update path with STM32 USB DFU |
| USB-C networking after boot | USB Ethernet gadget mode would provide convenient web-interface and SSH access, but is a future software choice rather than a present hardware requirement |

## Selected parts

| Use | Part | JLCPCB status when last checked |
|---|---|---|
| 4 V divider | R35: 15 kΩ, UNI-ROYAL 0603WAF1502T5E, C22809 | Basic |
| 4 V divider | R36: 10 kΩ, UNI-ROYAL 0603WAF1002T5E, C25804 | Basic |
| Guitar driver | R34/R37/R40: 1 kΩ, 0603WAF1001T5E, C21190 | Basic |
| Guitar driver | R38/R39: 470 Ω, 0603WAF4700T5E, C23179 | Basic |
| Codec coupling | C57/C58/C62/C63: Rubycon 16MF475KB23225, C50394238 | Extended; stock must be rechecked |
| 5 V buck | U6: TPS56637RPAR | Extended; compact 6 A synchronous buck |
| External USB switch | AP22653W6-7, 15 kΩ current-setting resistor | Reuse selected circuit; status must be rechecked |
| 3.3 V regulators | TPS7A2033PDBVR | Status must be rechecked |
| 10 V analog regulator | LT3045EMSE#PBF | Intended for manual assembly |

Basic/Extended classification and stock are time-dependent and must be checked again before ordering.

## Working rule

The assistant must not change design or project files without explicit approval. The normal workflow is discussion and instructions, followed by changes made by the project owner. Documentation-only edits also require approval.
