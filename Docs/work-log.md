# Work Log

## 2026-09-03 - Output direction selected

- Confirmed that the TAC5212 provides configurable headphone drivers rather than only line-level outputs.
- Selected a dedicated 3.5 mm stereo headphone output as a primary feature.
- Retained two unbalanced 6.35 mm line/amp outputs for normal pedalboard use.
- Chose the TAC5212 four-channel single-ended mode so both output pairs can operate without another amplifier IC.
- Left output coupling, filtering, protection, jack detection, and software configuration for later design.

## 2026-09-03 - Input circuits and documentation reviewed

- Verified the guitar input and balanced line-input paths against the saved schematic.
- Confirmed C56 is designed as 100 nF; incidental substitute values are not design decisions.
- Recorded the component values and concise reasoning for both input paths.
- Traced the original `audio_in_stereo` net to a planned GPIO27 jack-detection function.
- Confirmed the published Open Pedalboard software does not use that signal.
- Removed JP4 and the `audio_in_stereo` labels; CM GPIO27 is intentionally unconnected.
- Identified the TAC5212 core supplies, digital interface, and output circuit as the next unfinished audio block.

## 2026-09-03 - Previous design handover reviewed

- Reconciled the earlier ChatGPT design record with the live KiCad files.
- Recorded the two-layer constraint, 10 V analog rail, 4 V bias, and OPA1656 topology.
- Recorded the 100 nF film design capacitor and Rubycon codec-coupling capacitors.
- Recorded the Basic 1% resistor decision and exact known JLCPCB parts.
- Recorded the C/C++ firmware direction and provisional STM32G0B1KET6 candidate.
- Flagged the DNP guitar-input protection device as an open choice.

## 2026-09-02 - Project orientation

- Identified `PedalBoard-integrated-audio` as the active project.
- Treated the other Open Pedalboard folders as reference material.
- Confirmed that TAC5212 and OPA1656 work is present in the audio schematic.
- Confirmed that the PCB still represents the original plug-in sound-card design.
- Confirmed that the RP2040 controller section has not yet been replaced.
- Recorded the JLCPCB and manual-assembly preferences.
- Created the project documentation structure.

## Logging format

Future entries should contain only:

- Conclusions reached
- Decisions changed
- Work actually completed
- Important open questions
