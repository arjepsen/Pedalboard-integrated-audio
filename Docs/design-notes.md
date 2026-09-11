# Design Notes

Last updated: 2026-09-11

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
- Keep C24 at 10 µF on the general 3.3 V regulator output.
- Generate the quiet 10 V guitar-front-end rail with LT3045EMSE#PBF. Use 4.7 µF on SET, 100 kΩ for 10 V, 10 µF at its input, and 22 µF at its output.

The 10 V rail is retained because it gives the OPA1656 and TAC5212 input path comfortable signal headroom. The 5.08 V / 6 A supply provides useful CM5 and USB-current margin.

The general 3.3 V rail has been rechecked with the selected Waveshare displays. Each display is specified at about 46 mA maximum at 3.3 V, so the pair contributes about 92 mA. With the STM32 and the remaining small digital loads, the expected total is around 120 mA. The TPS7A2033 is rated for 300 mA, leaving comfortable current and thermal margin from the approximately 5.08 V input. C24 remains 10 µF because it is within the regulator's supported output-capacitance range and provides useful transient margin.

## Controller and displays

- U3 is an STM32G0B1CET6 in LQFP-48. It replaces the RP2040, its external QSPI flash, crystal, and associated support parts.
- Use the STM32's internal flash. No separate firmware flash is required.
- Use the internal HSI48 oscillator with USB clock recovery from the CM5 USB SOF signal. Firmware must enable CRS correctly; no external crystal is fitted.
- J15 and J17 connect to two Waveshare 2inch LCD Modules, SKU 17344. These are 2.0-inch 240×320 IPS displays using ST7789VW and 4-wire SPI.
- The modules are intended for straightforward screw/standoff mounting and use an 8-pin PH2.0 connector.
- J15 is the left display and J17 is the right display. Both use JST B8B-PH-K-S(LF)(SN) 8-pin, 2.0 mm vertical headers on the mainboard.
- The display connector pin order is BL, RESET, D/C, CS, SCK, MOSI, GND, 3V3. The shared signals are BL, RESET, D/C, SCK, and MOSI; each display has its own chip select.
- The Waveshare module includes its own 1 µF supply capacitor, so no extra local capacitor is fitted at J15/J17. The shared 3.3 V regulator retains C24 at 10 µF.
- The module contains the backlight switching transistor, so PA6 can drive both BL control inputs directly with PWM; an external backlight transistor is not required.
- Use DMA and small line/tile buffers rather than full framebuffers.
- Keep the display connectors on this controller/UI sheet. Their signals and power belong to the controller function, whereas the Connectivity sheet is for CM5 and external/service connections.
- Port the required controller behavior from the original Rust code to C/C++ while preserving its useful external USB-MIDI behavior and protocol.

### STM32 pin allocation

| Function | Pins |
|---|---|
| TFT shared SPI/control | PA5 SCK, PA7 MOSI, PA4 D/C, PB12 RESET, PA6 BL PWM |
| TFT chip selects | PB10 left, PB11 right |
| Addressable RGB LEDs | PA8 |
| Expression ADC inputs | PB0, PB1 |
| I2C | PB8 SCL, PB9 SDA |
| USB device | PA11 D−, PA12 D+ |
| SWD and reset | PA13 SWDIO, PA14 SWCLK, PF2 NRST |
| Optional DIN MIDI UART | PB6 TX, PB7 RX |
| Debug UART | PA9 TX, PA10 RX |
| Encoders | PA0–PA3, PB2, PB3 |
| Footswitches | PD0–PD3, PB4, PB5 |
| Boot/status | PC13 BOOT control, PB15 status LED |

Each expression input uses a 1 kΩ series resistor and 100 nF capacitor at the MCU. Retain the SWD/debug header and useful test points. The hierarchical `RGB_DATA` connection links U3 PA8 to the LED sheet.

### Controller EEPROM

Keep U12, the AT24CS01 EEPROM at I2C address `0x50`. The existing Open Pedalboard firmware stores active-preset state, per-preset switch state, and encoder values there. This avoids turning frequent runtime-state updates into STM32 internal-flash erase cycles. Its factory serial-number area is also useful for a stable board identity, even though the existing RP2040 firmware obtains its USB serial from the MCU flash ID instead.

## CM5-only connectivity

CM2 is now a Compute Module 5 using the `CM5IO:Raspberry-Pi-5-Compute-Module` footprint. The former FSUSB42 switch and USB2514B hub have been removed. CM5 exposes one USB 2.0 OTG connection and two native USB 3 host ports with USB 2.0 companion pairs, so the required USB 2.0 functions connect directly:

| CM5 connection | Board function |
|---|---|
| Legacy USB 2.0 OTG | USB-C CM5 service, recovery, and flashing |
| USB port 0 USB 2.0 pair | Internal STM32 USB-MIDI and firmware update |
| USB port 1 USB 2.0 pair | External USB-A host connector |

Leave the SuperSpeed pairs unused. The USB port 0 companion pair connects directly to U3; USB port 1 connects to the external USB-A connector; and the legacy USB 2.0 pair connects to the service USB-C connector. U11 provides external-connector ESD protection, and AP22653 provides switched, current-limited USB-A VBUS. Final checks still include USB role/recovery behavior, protection details, and a complete ERC review.

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
- The optional DIN MIDI group is marked DNP: J1–J4, J25, J26, L1–L4, R3–R5, D1, U2, C1, and C22.
- Keep the software routing options for DIN-to-USB and USB-to-DIN operation.
- Initial units can use `din_enabled: false`; the optional parts can be fitted later without revising the PCB.

The DNP state has been confirmed in the raw KiCad schematic and by exporting a BOM with DNP parts excluded.

## Manufacturing

- Primary manufacturer and assembler: JLCPCB.
- Basic parts are preferred when there is little or no disadvantage.
- Extended parts are acceptable when there is a clear benefit.
- Manual fitting is acceptable for a limited number of practical components.
- Use 0603 passives where practical.
- Fine-pitch and exposed-pad packages require suitable footprints, stencil design, and inspection.
- Write passive values without redundant unit letters: for example `100n`, `1u`, `4.7u`, `27R`, `1k5`, and `100k`.
- Standard sourcing fields are `Manufacturer`, `Manufacturer part`, and `JLCPCB part`. Use KiCad's native footprint, datasheet, description, and DNP properties instead of duplicating them as custom fields.
- Perform a complete field and sourcing audit after the circuit topology is stable and before PCB/BOM release. Exact production part numbers are most useful for ICs, connectors, special capacitors, and any passive whose dielectric, voltage rating, or tolerance matters.

## Important project state

- `audio.kicad_sch` and `psu.kicad_sch` contain the current custom work.
- The guitar input, line input, TAC5212 support, digital-audio connections, and selected output circuits are present in the schematic.
- The updated power architecture is present in the schematic.
- The STM32 controller, both Waveshare display connectors, optional DIN MIDI circuit, and direct CM5 USB arrangement are present in the schematic.
- The general 3.3 V rail has been checked with the selected displays and the TPS7A2033 is retained.
- A whole-project component-value and sourcing-field audit remains to be done after the schematic topology is stable.
- The PCB has not yet been updated for the integrated audio circuit.
