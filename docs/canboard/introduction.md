[![Donate](https://img.shields.io/badge/Donate-PayPal-blue.svg)](https://www.paypal.com/donate/?hosted_button_id=HDE8YCVY9NR2L)
[![Discord](https://img.shields.io/discord/1243358184266010667?label=discord)](https://discord.com/invite/TxnPYQkVu3)
[![GitHub Release](https://img.shields.io/github/v/release/corygrant/CANBoard?include_prereleases&display_name=tag)](https://github.com/corygrant/CANBoard/releases)

## What Is It?

CANBoard is a small CAN enabled I/O board, designed for steering wheels, button boxes, switch panels, etc. It reads switches and knobs and sends their state on CAN, and it can drive small loads like indicator lights.

CANBoard runs [dingoFW](../dingofw/introduction.md), so it has the same logic functions as dingoPDM (CAN inputs and outputs, virtual inputs, conditions, counters, flashers) and is configured with [dingoConfig](../dingoconfig/canboard.md).

- Hardware: [CANBoard repo](https://github.com/corygrant/CANBoard)
- [Store](https://dingo-electronics.square.site/product/canboard/3)

## Features

- 8 digital inputs
    - Ground switching
- 5 analog inputs
    - 0 to 5V
    - Can be used as voltage, rotary switch or switch inputs
- 4 digital outputs
    - Low side switch (open collector)
    - 0.5A max each
- `STM32F303K8` microcontroller
- MCU temperature measurement
- Optional 120Ω CAN terminating resistor (solder jumper)

## Configuration

| Function         | Count |
| ---------------- | ----- |
| Digital inputs   | 8     |
| Analog inputs    | 5     |
| Digital outputs  | 4     |
| CAN inputs       | 8     |
| CAN outputs      | 8     |
| Virtual inputs   | 8     |
| Conditions       | 8     |
| Counters         | 4     |
| Flashers         | 4     |

CANBoard has no USB connection, it is configured over CAN with a USB to CAN [adapter](../dingoconfig/adapters.md) or through the USB port of a dingoPDM on the same bus (see [USB](../dingoconfig/adapters.md#usb)). The configuration is saved in the MCU's internal flash.

CANBoard does not sleep and has no status LEDs.

## CAN

The default Base ID is `0x640` (1600) and the default bitrate is `500K`. Both can be changed in dingoConfig, see [Changing the CAN Base ID](../dingopdm/introduction.md#changing-the-can-base-id).

!!! Note
    The CAN ID solder jumpers on the PCB were used by the old CANBoard firmware. dingoFW does not read them, the Base ID is set in dingoConfig.

**Jumpers**

| Jumper       | Function |
| ------------ | -------- |
| CAN Term     | Solder to enable the 120Ω terminating resistor across CANL/CANH |

## Analog Inputs

Each analog input creates 4 [variables](../dingofw/variablemap.md#canboard): raw ADC value, millivolts, rotary position and switch state.

| Setting             | Description |
| ------------------- | ----------- |
| Enabled             | Input is read |
| Switch Enabled      | Use the input as an on/off switch |
| Switch Mode         | `Momentary` or `Latching` |
| Switch Invert       | Invert the switch |
| Switch Threshold    | Switch is on above this voltage (mV) |
| Rotary Enabled      | Use the input as a rotary switch with a resistor ladder |
| Rotary Invert       | Reverse the position order |
| Rotary Offset       | Voltage at position 0 (mV) |
| Rotary Step         | Voltage between positions (mV) |
| Rotary Max Position | Highest position |

Rotary position = (mV - Offset) / Step, limited to 0 to Max Position.

!!! Example
    A 12 position rotary switch with a resistor ladder from 0V to 5V, about 455mV per step:

    - Rotary Offset = `0`
    - Rotary Step = `455`
    - Rotary Max Position = `11`

## Digital Outputs

| Setting | Description |
| ------- | ----------- |
| Enabled | Output is used |
| Input   | [Variable](../dingofw/variablemap.md#canboard) that turns the output on |

## CAN Messages

CANBoard sends these messages every `100ms`. Message _n_ is sent on `Base ID + 2 + n`. All messages are 8 bytes, little endian.

| Msg | CAN ID       | Always Sent    | Byte 0 | Byte 1 | Byte 2 | Byte 3 | Byte 4 | Byte 5 | Byte 6 | Byte 7 |
| :-: | :----------: | :------------: | :----: | :----: | :----: | :----: | :----: | :----: | :----: | :----: |
| 0   | Base ID + 2  | :green_square: | AI1    | AI1    | AI2    | AI2    | AI3    | AI3    | AI4    | AI4    |
| 1   | Base ID + 3  | :green_square: | AI5    | AI5    | 0      | 0      | 0      | 0      | T      | T      |
| 2   | Base ID + 4  | :green_square: | RS12   | RS34   | RS5    | FL     | DI     | AS     | DO     | HB     |
| 3   | Base ID + 5  |                | CI     | CI     | CI     | CI     | VI     | VI     | VI     | VI     |
| 4   | Base ID + 6  |                | CNT1   | CNT2   | CNT3   | CNT4   | CON    | CON    | CON    | CON    |
| 5   | Base ID + 7  |                | CIV1   | CIV1   | CIV1   | CIV1   | CIV2   | CIV2   | CIV2   | CIV2   |
| 6   | Base ID + 8  |                | CIV3   | CIV3   | CIV3   | CIV3   | CIV4   | CIV4   | CIV4   | CIV4   |
| 7   | Base ID + 9  |                | CIV5   | CIV5   | CIV5   | CIV5   | CIV6   | CIV6   | CIV6   | CIV6   |
| 8   | Base ID + 10 |                | CIV7   | CIV7   | CIV7   | CIV7   | CIV8   | CIV8   | CIV8   | CIV8   |

- `AIn` - Analog input _n_ (mV)
- `T` - MCU temperature (°C)
- `RSxy` - Rotary switch positions, bits 0-3 = analog input _x_, bits 4-7 = analog input _y_
- `RS5` - Rotary switch position, analog input 5
- `FL` - Flashers, bit 0 to 3 = flasher 1 to 4
- `DI` - Digital inputs, bit 0 to 7 = input 1 to 8
- `AS` - Analog input switches, bit 0 to 4 = analog input 1 to 5
- `DO` - Digital outputs, bit 0 to 3 = output 1 to 4
- `HB` - Heartbeat, counts up every message
- `CI` - CAN input results, bit 0 = CAN input 1 ... bit 7 = CAN input 8
- `VI` - Virtual input results, bit 32 = virtual input 1 ... bit 39 = virtual input 8
- `CNTn` - Counter _n_ value
- `CON` - Condition results, bit 32 = condition 1 ... bit 39 = condition 8
- `CIVn` - CAN input _n_ value, 32 bits

Messages 3 to 8 are only sent when their functions are enabled. See [CAN Protocol](../dingofw/can.md) for the configuration messages.

## Firmware Updating

CANBoard is programmed with an ST-Link and a Tag-Connect TC2030 cable, see [Updating](../dingofw/updating.md#canboard).

## BOM

[CANBoard BOM](../bom/CANBoard/ibom.html)
