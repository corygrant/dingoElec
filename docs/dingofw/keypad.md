# Keypads

## Introduction

dingoPDM and dingoPDM-Max support up to 2 CAN keypads per device. The device acts as the keypad's CANopen master node.

As the keypad master the device will:

- Send the CANopen start message
- Read the button, dial and analog input states
- Set the backlight color and brightness
- Set the button LED colors, based on any [variables](./variablemap.md)

Each keypad needs only 1 master node. 

Keypad messages can also be read by other devices with [CAN inputs](./can.md#can-inputs), without being the master node.

Keypads are set up in dingoConfig, see [dingoConfig Keypads](../dingoconfig/keypads.md).

## Models

- :green_square: : Tested
- :yellow_square: : Supported, Untested

| Brand          | Model            | Buttons | Dials | Firmware Model #       | Status          |
| :------------- | :--------------- | :-----: | :---: | :--------------------- | :-------------: |
| `Blink Marine` | `PKP-1100-SI`    | 1       |       | 10 `Blink1Key`         | :yellow_square: |
| `Blink Marine` | `PKP-1200-SI`    | 2       |       | 0 `Blink2Key`          | :yellow_square: |
| `Blink Marine` | `PKP-2200-SI`    | 4       |       | 1 `Blink4Key`          | :yellow_square: |
| `Blink Marine` | `PKP-1500-SI`    | 5       |       | 2 `Blink5Key`          | :yellow_square: |
| `Blink Marine` | `PKP-1600-SI`    | 6       |       | 3 `Blink6Key`          | :yellow_square: |
| `Blink Marine` | `PKP-2300-SI`    | 6       |       | 3 `Blink6Key`          | :yellow_square: |
| `Blink Marine` | `PKP-2400-SI`    | 8       |       | 4 `Blink8Key`          | :yellow_square: |
| `Blink Marine` | `PKP-2500-SI`    | 10      |       | 5 `Blink10Key`         | :yellow_square: |
| `Blink Marine` | `PKP-2600-SI`    | 12      |       | 6 `Blink12Key`         | :green_square:  |
| `Blink Marine` | `PKP-3500-SI`    | 15      |       | 7 `Blink15Key`         | :yellow_square: |
| `Blink Marine` | `PKP-3500-SI-MT` | 15      | 2     | 8 `Blink15Key2Dial`    | :yellow_square: |
| `Grayhill`     | `3Kx06`          | 6       |       | 20 `Grayhill6Key`      | :yellow_square: |
| `Grayhill`     | `3Kx08`          | 8       |       | 21 `Grayhill8Key`      | :yellow_square: |
| `Grayhill`     | `3Kx12`          | 12      |       | 22 `Grayhill12Key`     | :yellow_square: |
| `Grayhill`     | `3Kx15`          | 15      |       | 23 `Grayhill15Key`     | :yellow_square: |
| `Grayhill`     | `3Kx20`          | 20      |       | 24 `Grayhill20Key`     | :yellow_square: |

!!! Note
    If you've tested one of the :yellow_square: marked models above please let me know

## Initial Setup

### Setting Node ID

Each keypad on the bus needs its own CANopen node ID. Use the same node ID for the keypad device in dingoConfig.

Blink Marine keypads ship with node ID `0x15` (21) by default.

### Bitrate

The keypad and the device must use the same CAN bitrate. Blink Marine keypads default to `125K`, change the keypad or the device bitrate so they match.

## Settings

| Setting                  | Description |
| ------------------------ | ----------- |
| Enabled                  | Keypad is used |
| Node ID                  | CANopen node ID of the keypad |
| Model                    | Keypad model, sets the number of buttons/dials |
| Timeout Enabled          | Reset all buttons, dials and analog inputs when the keypad stops sending |
| Timeout                  | Time without a message before reset (ms) |
| Backlight Brightness     | 0 to 63 |
| Dim Backlight Brightness | 0 to 63, used while the dimming input is true |
| Backlight Color          | See [Backlight Colors](#backlight-colors) |
| Dimming Input            | Variable that switches to the dim brightness |
| Button Brightness        | 0 to 63 |
| Dim Button Brightness    | 0 to 63, used while the dimming input is true |

### Buttons

Each button creates a [variable](./variablemap.md) (`Keypad n Button n`) that can be used by any function.

| Setting                | Description |
| ---------------------- | ----------- |
| Enabled                | Button is used |
| Mode                   | `Momentary` or `Latching` |
| Value Var 0 - 3        | Variables that pick the LED color. If more than one is on (`1`), the highest numbered one wins |
| Color 0 - 3            | LED color for each value var |
| Blink 0 - 3            | Blink the LED for each value var |
| Blink Color 0 - 3      | Second color used when blinking |
| Fault Var              | Variable that shows the fault color, for example `Output 1 Fault`. Fault takes priority over the value vars |
| Fault Color            | LED color when the fault var is on |
| Fault Blink            | Blink the LED when the fault var is on |
| Fault Blink Color      | Second color used when the fault is blinking |

!!! Example
    Button 1 turns on output 1 with a latching button. The LED is green when the output is on and red when the output faults.

    - Button 1: `Enabled`, Mode = `Latching`
    - Output 1: Input = `Keypad 1 Button 1`
    - Button 1 Value Var 0 = `None`, Color 0 = `Off`
    - Button 1 Value Var 1 = `Output 1 On`, Color 1 = `Green`
    - Button 1 Fault Var = `Output 1 Fault`, Fault Color = `Red`

### Dials

Blink Marine keypads with dials (`PKP-3500-SI-MT`) create a variable per dial (`Keypad n Dial n`).

| Setting    | Description |
| ---------- | ----------- |
| Enabled    | Dial is used |
| Min Count  | Lowest dial value (0 to 16) |
| Max Count  | Highest dial value (0 to 16) |
| LED Offset | Shift the dial LED ring position (0 to 16) |

## Colors

### Button Colors

Blink Marine button LEDs.

Grayhill buttons have up to 3 single color LEDs. The color number is used as bits: bit 0 = LED 1, bit 1 = LED 2, bit 2 = LED 3 (for example `5` = LED 1 and LED 3).

| # | Color    |
| - | -------- |
| 0 | `Off`    |
| 1 | `Red`    |
| 2 | `Green`  |
| 3 | `Orange` |
| 4 | `Blue`   |
| 5 | `Violet` |
| 6 | `Cyan`   |
| 7 | `White`  |

### Backlight Colors

| # | Color          |
| - | -------------- |
| 0 | `Off`          |
| 1 | `Red`          |
| 2 | `Green`        |
| 3 | `Blue`         |
| 4 | `Yellow`       |
| 5 | `Cyan`         |
| 6 | `Violet`       |
| 7 | `White`        |
| 8 | `Amber`        |
| 9 | `Yellow Green` |

## CAN Filters

If `CAN Filters Enabled` is on, the device automatically adds the keypad IDs (button state, and for Blink Marine the dial and analog input IDs) to the hardware filter.
