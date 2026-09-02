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

## Working rule

The assistant must not change design or project files without explicit approval. The normal workflow is discussion and instructions, followed by changes made by the project owner.

