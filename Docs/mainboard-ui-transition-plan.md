# Mainboard UI Transition Plan

> This is a manual KiCad checklist. It records agreed changes but does not authorize automatic schematic edits.

**Goal:** Keep the CM5, audio, power, and external connectivity on the mainboard while moving the physical user interface to a later ESP32-S3 UI board.

**Architecture:** The CM5 remains responsible for audio, MOD, PiPedal, and system state. A separate UI board will later contain the ESP32-S3, display, encoders, switches, expression input, LEDs, and optional MIDI interface. The mainboard supplies 5 V and communicates with the UI board over UART.

**Scope:** Mainboard schematic changes only. The UI-board schematic and PCB are deliberately deferred.

## Hardware changes in short form

- Remove the STM32 controller subsystem from the active mainboard design.
- Remove the mainboard display, controls, expression, MIDI, and LED-interface circuitry that will move to the UI board.
- Add one keyed 2x5 IDC connector for UI power, UART, reset, and boot control.
- Free the CM5 USB port currently assigned to the STM32.
- Keep the existing CM5 USB-C service port and use it for both recovery flashing and USB Ethernet networking.
- Keep Wi-Fi as an option, using the CM5 wireless version and an external antenna in the aluminium enclosure.
- Keep the TAC5212, guitar input, optional line input, audio outputs, CM5, and established power supplies.
- Do not remove U5 during the controller cleanup. It still powers TAC5212 IOVDD in the present design and requires a separate power-domain decision.

## Mainboard-to-UI connector

Use a keyed and shrouded 2.54 mm IDC system.

| Item | Selection |
|---|---|
| PCB header | Würth Elektronik 61201021621 |
| Cable socket | Würth Elektronik 61201023021, with strain relief |
| Cable | 10-conductor, 28 AWG ribbon cable, approximately 100 mm |
| Assembly | Header marked DNP for JLCPCB and fitted by hand |

Pin numbering must follow the connector datasheet and footprint pin-1 mark.

| Pin | Signal | Purpose |
|---:|---|---|
| 1 | `+5V_UI` | UI power, connected to mainboard `+5VP` |
| 2 | GND | Power return |
| 3 | `+5V_UI` | Second power contact, connected to mainboard `+5VP` |
| 4 | GND | Power return |
| 5 | `CM5_UART_TX` | CM5 to ESP32-S3 |
| 6 | GND | Signal return |
| 7 | `CM5_UART_RX` | ESP32-S3 to CM5 |
| 8 | GND | Signal return |
| 9 | `UI_RESET_REQ` | CM5 requests ESP32-S3 reset |
| 10 | `UI_BOOT_REQ` | CM5 requests ESP32-S3 download mode |

`UI_RESET_REQ` and `UI_BOOT_REQ` are active-high requests. The later UI board will use local MOSFETs and pull-down resistors to pull ESP32-S3 EN and GPIO0 low. This prevents the CM5 from directly driving ESP pins across different power states.

## Mainboard schematic steps

### 1. Preserve a known baseline

- [ ] Commit and push the current schematic before beginning the transition.
- [ ] Run ERC and record the starting warning count.
- [ ] Confirm that the current PCB has not yet been updated for the new controller or audio design.

### 2. Add the UI connector on the Connectivity page

- [ ] Place a generic `Conn_02x05_Odd_Even` symbol on `connect.kicad_sch`.
- [ ] Assign the verified Würth 61201021621 footprint or import Würth's official KiCad footprint.
- [ ] Set `Value` to `UI LINK 2x5`.
- [ ] Set `Manufacturer` to `Würth Elektronik`.
- [ ] Set `Manufacturer part` to `61201021621`.
- [ ] Leave `JLCPCB part` empty.
- [ ] Mark the connector DNP.
- [ ] Add the note `Hand-fit. Mates with 61201023021 IDC socket and 10-conductor 28 AWG ribbon cable.`
- [ ] Wire pins 1 and 3 to the mainboard `+5VP` rail.
- [ ] Wire pins 2, 4, 6, and 8 to GND.
- [ ] Wire pins 5, 7, 9, and 10 according to the table above.

The mainboard does not need another UI power regulator. The UI board will receive 5 V and provide its own local 3.3 V regulation and decoupling.

### 3. Connect the CM5 control signals

- [ ] Keep `CM5_UART_TX` on CM2 pin 55, GPIO14.
- [ ] Keep `CM5_UART_RX` on CM2 pin 51, GPIO15.
- [ ] Remove the no-connect flag from CM2 pin 46, GPIO22, and label it `UI_RESET_REQ`.
- [ ] Remove the no-connect flag from CM2 pin 47, GPIO23, and label it `UI_BOOT_REQ`.
- [ ] Retain J16 as the DNP CM5 UART debug header.
- [ ] Add a note that J16 must not be connected to another transmitting device while the UI board is attached.

No level shifter is required because the CM5 GPIO and ESP32-S3 UART use 3.3 V logic.

### 4. Remove the old mainboard controller blocks

- [ ] Remove the active `MIDI` hierarchical-sheet instance that points to `pico.kicad_sch`.
- [ ] Remove the active `LEDs` hierarchical-sheet instance that points to `leds.kicad_sch`.
- [ ] Remove their obsolete hierarchical pins and connections from the root schematic, including `RGB_DATA`.
- [ ] Confirm that U3 STM32G0B1, U12 EEPROM, J12 SWD, J15/J17 displays, local switches, encoders, expression inputs, and MIDI circuits are no longer in the active mainboard hierarchy.
- [ ] Confirm that U1, C80, R7, R10, and J28 from the LED interface are no longer in the active mainboard hierarchy.
- [ ] Keep `pico.kicad_sch` and `leds.kicad_sch` as temporary reference files until the later UI project is created. Do not copy their old circuits blindly into the ESP32-S3 design.

### 5. Free the CM5 USB port previously used by STM32

- [ ] Remove the `USB_STM32_P` and `USB_STM32_N` labels and wiring.
- [ ] Add no-connect flags to CM2 pin 134, `USB3-0-DP`, and pin 136, `USB3-0-DM`.
- [ ] Leave the unused SuperSpeed signals for that port unconnected as they are now.

This USB port remains spare. The UI board uses UART, not USB.

### 6. Retain and relabel the CM5 service USB-C function

No electrical change is required around J9.

- [ ] Keep J9 D+ and D− connected to CM5 USB2 through U16.
- [ ] Keep R30 and R31 at 5.1 kΩ on CC1 and CC2.
- [ ] Keep J9 VBUS unconnected from the mainboard power rails.
- [ ] Keep CM2 pin 101, `USB_OTG_ID`, unconnected for device operation.
- [ ] Keep J11 pulling `nRPIBOOT` low for CM5 recovery and eMMC flashing.
- [ ] Update the J9 note to `Self-powered CM5 USB 2.0 service port for rpiboot flashing, USB Ethernet, and SSH. VBUS intentionally unconnected.`

Normal boot will allow USB Ethernet gadget networking. Booting with J11 fitted will instead enter CM5 recovery mode.

### 7. Recheck the power budget after removing STM32

- [ ] Keep the TLVM13660 5 V supply and its present component values.
- [ ] Reserve up to 1 A at 5 V for the complete UI board through the two power contacts.
- [ ] Include CM5 peak demand up to 2.5 A and the external USB-A limit of approximately 1.74 A in the total 5 V calculation.
- [ ] Confirm that expected codec, regulator, fan, and UI loads stay within the practical thermal capability of the 6 A supply and four-layer PCB layout.
- [ ] Do not remove U5 or change TAC5212 IOVDD during this controller transition. Review that power domain separately.

### 8. Record the Wi-Fi arrangement

- [ ] Use a wireless CM5 variant if Wi-Fi is wanted.
- [ ] Keep physical access to the CM5 U.FL connector.
- [ ] Reserve a possible enclosure position for the official Raspberry Pi external antenna kit.
- [ ] Do not add RF traces or an antenna connector to the mainboard schematic.

The external antenna is optional hardware. USB Ethernet through J9 remains the dependable wired control connection.

### 9. Verify before moving to PCB work

- [ ] Run ERC and resolve new errors caused by removed sheets or labels.
- [ ] Confirm that `USB_STM32_P`, `USB_STM32_N`, and `RGB_DATA` no longer appear in the active schematic.
- [ ] Confirm that GPIO22 and GPIO23 connect only to their corresponding UI request nets.
- [ ] Confirm that all ten UI connector pins match the agreed table.
- [ ] Confirm that the UI connector is DNP and has the correct manufacturer fields.
- [ ] Confirm that J9 VBUS and CM5 `USB_OTG_ID` remain intentionally unconnected.
- [ ] Commit and push the completed mainboard schematic transition.

## Deferred UI-board work

The later UI project will contain:

- ESP32-S3-WROOM-1U-N8R2 and its local 3.3 V supply.
- Display connector and display power control.
- Encoders, footswitch inputs, one expression input, and LEDs.
- Optional chassis-wired MIDI input and output circuitry.
- Local ESP32-S3 reset and boot buttons.
- MOSFET interfaces for `UI_RESET_REQ` and `UI_BOOT_REQ`.
- The matching 2x5 Würth connector, also hand-fitted and DNP for JLCPCB.

The UI schematic can wait until its display and mechanical layout are settled. Its board outline, display window, encoder positions, switch wiring, and cable exit must still be established before the mainboard PCB placement is frozen.

## Repository recommendation

Keep the UI PCB in this repository as a separate KiCad project, for example under `ui-board/`. The two PCBs share a cable pinout, enclosure, firmware interface, and release version, so one repository makes mismatches less likely. A separate GitHub repository would only be useful if the UI board were intended to become an independent reusable product.

## Decisions still separate from this plan

- Final display/module selection and its mechanical arrangement.
- Final UI-board 3.3 V regulator and display backlight power circuit.
- Whether TAC5212 IOVDD remains on U5 or later moves to a CM5-related power domain.
- Final enclosure antenna location and cooling arrangement.
