# Design Notes

Last updated: 2026-09-09

## Audio architecture

- Selected codec: TAC5212IRGER, mounted directly on the mainboard.
- Primary use: guitar effects and NAM playback using Open Pedalboard or PiPedal software. Must be compatible with both.
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

This arrangement matches the primary desk-practice use without adding a separate headphone-amplifier IC. The single-ended line outputs give lower specified dynamic range than differential mode, but remain well above the expected system requirement. Final register settings and practical testing remain open.

## Power architecture

- Specify a regulated 12 V input with ±10% tolerance.
- Retain the 4 A / 15 V input PTC and FDS4435BZ reverse-polarity protection.
- Generate approximately 5.08 V with TPS56637RPAR, a 3.3 µH inductor, and 68 kΩ / 9.1 kΩ feedback resistors. This rail supplies the CM and downstream regulators.
- Use AP22653W6-7 to switch and current-limit the external USB-A VBUS to approximately 1.5 A. Retain its local bulk capacitance for USB load steps.
- Generate the general digital 3.3 V and `3V3_CODEC` analog rail with separate TPS7A2033PDBVR regulators.
- Generate the quiet 10 V guitar-front-end rail with LT3045EMSE#PBF. Use 4.7 µF on SET, 100 kΩ for 10 V, 10 µF at its input, and 22 µF at its output.

The 10 V rail is retained because it gives the OPA1656 and TAC5212 input path comfortable signal headroom. The 5.08 V / 6 A supply provides useful CM5 and USB-current margin.

## Controller

- The original design uses an RP2040, external QSPI flash, crystal, and supporting parts.
- The selected replacement is STM32G0B1CET6 in LQFP-48.
- Two 2.0-inch HS20S010B displays use ST7789V2 controllers and share an SPI bus with separate chip selects.
- Use DMA and small line/tile buffers rather than full framebuffers.
- Fixed functions cover TFT SPI/control, RGB LED data, expression ADCs, I2C, USB, SWD, reset, and crystal pins. Footswitch and encoder GPIOs remain flexible for routing.
- Drive the TFT backlights through an external power stage, not directly from an MCU pin.
- Port the required controller behavior from the original Rust code to C/C++.

## CM5-only connectivity direction

The saved connectivity sheet still contains the original CM4-compatible USB arrangement:

- The CM's single legacy USB 2.0 pair enters U8, an FSUSB42 USB switch.
- U8 selects either the USB-C service/boot connector or U9, a USB2514B four-port hub.
- One U9 downstream port serves the controller as USB-MIDI and another serves the external USB-A connector.
- The AP22653 load switch controls external USB-A power.

The hub and switch are required by CM4 because one native USB connection has to cover three roles. CM5 exposes one USB 2.0 OTG connection and two native USB 3 host ports with USB 2.0 companion pairs. Use them directly:

| CM5 connection | Board function |
|---|---|
| Legacy USB 2.0 OTG | USB-C CM5 service, recovery, and flashing |
| USB port 0 USB 2.0 pair | Internal STM32 USB-MIDI and firmware update |
| USB port 1 USB 2.0 pair | External USB-A host connector |

Leave the SuperSpeed pairs unused. U8 and U9 are not functions that have been integrated as identical chips; they become unnecessary because CM5 provides enough independent native USB connections.

This direction removes the need for U8, U9, their crystal, and most hub support parts. Retain a switched, current-limited 5 V supply for external USB-A and add appropriate ESD protection at both external USB connectors. Exact role control, `VBUS_EN`, ESD parts, and schematic edits still require final verification.

The current schematic still uses a CM4 symbol, CM4 footprint, and names such as `CM4_3V` and `CM4_GPIO...`. Replace these with a verified CM5 symbol/footprint and CM5-specific names before rewiring USB. Do not merely rename the existing symbol: first verify every used power, USB, I2S, I2C, GPIO, reset, boot, and LED pin against the CM5 documentation.

## USB software compatibility

- Open Pedalboard uses the internal controller as a bidirectional class-compliant USB-MIDI device. It carries performance MIDI, controller configuration, state/feedback, and bootloader commands.
- Preserve the existing useful controller behaviour and custom configuration protocol in the STM32 implementation.
- Pedalboard OS currently identifies the RP2040 by its Raspberry Pi USB VID/PID and product name. The STM32 migration requires a new legitimate USB identity, stable serial number, updated udev matching, and updated bridge matching.
- The present firmware updater expects the RP2040 `RPI-RP2` mass-storage bootloader and a UF2 file. Replace this with an STM32G0B1 USB-DFU update path.
- PiPedal accepts the STM32 as a standard MIDI input and can bind it to presets, snapshots, plugin controls, hotspot control, shutdown, and reboot.
- PiPedal currently provides no MIDI output feedback. Basic operation needs no PiPedal changes, but dynamic preset names and state on the TFTs would require a future CM5 companion service.
- External USB-A remains useful for optional MIDI controllers, USB audio interfaces, and normal Linux USB peripherals.
- USB Ethernet gadget mode on the service USB-C port is a useful future option for web access, SSH, and file transfer, but is not required for the initial schematic.

## MIDI

- Preserve the original DIN MIDI input and output capability in the schematic, PCB, and STM32 pin allocation.
- Initially mark the DIN connectors and avoidable surrounding interface parts DNP.
- Keep the software routing options for DIN-to-USB and USB-to-DIN operation.
- Initial units can use `din_enabled: false`; the optional parts can be fitted later without revising the PCB.

## Manufacturing

- Primary manufacturer and assembler: JLCPCB.
- Basic parts are preferred when there is little or no disadvantage.
- Extended parts are acceptable when there is a clear benefit.
- Manual fitting is acceptable for a limited number of practical components.
- Use 0603 passives where practical.
- Fine-pitch and exposed-pad packages require suitable footprints, stencil design, and inspection.

## Important project state

- `audio.kicad_sch` and `psu.kicad_sch` contain the current custom work.
- The guitar input, line input, TAC5212 support, digital-audio connections, and selected output circuits are present in the schematic.
- The updated power architecture is present in the schematic.
- The PCB has not yet been updated for the integrated audio circuit.
- The controller and connectivity sheets still contain the original RP2040 and CM4-compatible USB subsystems; both are awaiting the confirmed CM5-only redesign.
- CM1 is still represented by the original CM4 symbol/footprint and CM4-specific net names. Verifying and replacing this representation is the first connectivity change.
