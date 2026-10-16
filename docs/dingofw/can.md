# CAN Protocol

!!! Info
    This page describes dingoFW `v0.6.x`. Older firmware (`v0.4` and earlier) used a different, prefix based settings protocol that is no longer supported.

    USB uses the same frames as CAN. Over USB, frames are wrapped in SLCAN style ASCII (`t<ID><DLC><DATA>\r`), so the same messages work on both connections.

## IDs

Every device uses a block of standard (11-bit) CAN IDs, starting at the **Base ID**.

| CAN ID            | Direction            | Use                                                        |
| ----------------- | -------------------- | ---------------------------------------------------------- |
| `Base ID + 0`     | Device → dingoConfig | Parameter responses, request responses, info/warning/error messages |
| `Base ID + 1`     | dingoConfig → Device | Parameter commands and requests (burn, sleep, bootloader, etc.) |
| `Base ID + 2` ... | Device → bus         | [Cyclic status messages](#cyclic-messages)                 |

| Device       | Default Base ID   | Cyclic messages                  |
| ------------ | ----------------- | -------------------------------- |
| dingoPDM     | `0x0DE` (222)     | 27 (`Base ID + 2` to `Base ID + 28`) |
| dingoPDM-Max | `0x0DE` (222)     | 27 (`Base ID + 2` to `Base ID + 28`) |
| CANBoard     | `0x640` (1600)    | 9 (`Base ID + 2` to `Base ID + 10`)  |

!!! Warning
    When using more than one device on the same bus, give each device a Base ID far enough apart that the ID blocks don't overlap. 

    A dingoPDM uses 29 IDs (`Base ID + 0` to `Base ID + 28`).

The Base ID and bitrate are changed in dingoConfig, see [Changing the Base ID](../dingopdm/introduction.md#changing-the-can-base-id).

Supported bitrates: `1000K`, `500K` (default), `250K`, `125K`, `100K`

## DBC

DBC files are in the firmware repo: [dingoFW/dbc](https://github.com/corygrant/dingoFW/tree/master/dbc)

The DBC files use the default Base IDs (`0x0DE` for dingoPDM/dingoPDM-Max, `0x640` for CANBoard). If you change the Base ID, shift the message IDs by the same amount.

!!! Note
    CAN input values (`CIVn`) are encoded with each CAN input's own factor and offset, so the DBC files show them as raw 32-bit values.

## CAN Inputs

CAN inputs read a signal out of any CAN message on the bus. The signal is defined the same way as a DBC signal.

| Setting      | Description |
| ------------ | ----------- |
| Enabled      | Input is evaluated |
| ID Type      | `0` = Standard (11-bit), `1` = Extended (29-bit) |
| ID           | CAN ID to read |
| Start Bit    | First bit of the signal (0 to 63) |
| Bit Length   | Length of the signal (1 to 32 bits) |
| Byte Order   | `0` = Little Endian (Intel), `1` = Big Endian (Motorola) |
| Signed       | Signal is a signed value |
| Factor       | Value = (raw × Factor) + Offset |
| Offset       | Value = (raw × Factor) + Offset |
| Operator     | Compares the value to the `Operand` |
| Operand      | Value to compare against |
| Mode         | `Momentary` or `Latching` |
| Timeout Enabled | Reset the input when messages stop |
| Timeout      | Time without a message before the input resets (ms) |

Each CAN input creates two [variables](./variablemap.md):

- **CAN In _n_**: result of the comparison, `0` or `1`
- **CAN In Val _n_**: the scaled signal value

!!! Warning
    If the timeout is enabled, the message must be received at least every timeout interval or both the value and the result will be set to `0`.

**Operators**

| # | Operator                | Result true when            |
| - | ----------------------- | --------------------------- |
| 0 | `Equal`                 | Value == Operand            |
| 1 | `Not Equal`             | Value != Operand            |
| 2 | `Greater Than`          | Value > Operand             |
| 3 | `Less Than`             | Value < Operand             |
| 4 | `Greater Than or Equal` | Value >= Operand            |
| 5 | `Less Than or Equal`    | Value <= Operand            |
| 6 | `Bitwise And`           | (Value & Operand) != 0      |
| 7 | `Bitwise Nand`          | (Value & Operand) == 0      |

**Modes**

- `Momentary`: result follows the comparison
- `Latching`: result toggles every time the comparison becomes true

### CAN Input Examples

- Blink Marine keypad button 1 (CANopen node 0x15, message `0x195`, byte 0, bit 0)
    - ID = `0x195`
    - Start Bit = `0`
    - Bit Length = `1`
    - Operator = `Equal`
    - Operand = `1`
    - Mode = `Latching` or `Momentary`

!!! Tip
    Supported keypads can be set up as [keypads](./keypad.md) instead, which also controls the button LEDs.

- CANBoard rotary switch 2 at position 3 (Base ID `0x640`, message `0x644` = `Base ID + 4`, byte 0 high nibble)
    - ID = `0x644`
    - Start Bit = `4`
    - Bit Length = `4`
    - Operator = `Equal`
    - Operand = `3`
    - Mode = `Momentary`

- CANBoard analog input 2 above 500mV (Base ID `0x640`, message `0x642` = `Base ID + 2`, bytes 2-3)
    - ID = `0x642`
    - Start Bit = `16`
    - Bit Length = `16`
    - Operator = `Greater Than`
    - Operand = `500`
    - Mode = `Momentary`

## CAN Outputs

CAN outputs send any [variable](./variablemap.md) out on the bus, encoded like a DBC signal.

| Setting    | Description |
| ---------- | ----------- |
| Enabled    | Output is sent |
| Input      | Variable to send |
| ID Type    | `0` = Standard (11-bit), `1` = Extended (29-bit) |
| ID         | CAN ID to send on |
| Start Bit  | First bit of the signal (0 to 63) |
| Bit Length | Length of the signal (1 to 32 bits) |
| Byte Order | `0` = Little Endian (Intel), `1` = Big Endian (Motorola) |
| Signed     | Signal is a signed value |
| Factor     | raw = (Value - Offset) / Factor |
| Offset     | raw = (Value - Offset) / Factor |
| Interval   | Time between messages (ms), `0` = 100ms |

CAN outputs that use the same ID are packed into one message. 
The message length (DLC) is set by the signal that ends furthest into the message, and the message uses the shortest interval of its signals.

## Cyclic Messages

The device sends status messages every `100ms`. Message _n_ is sent on `Base ID + 2 + n`. All messages are 8 bytes, little endian.

!!! Note
    Messages not marked as always sent are only sent when one of their functions is enabled.

!!! Note
    Cyclic messages pause while dingoConfig is reading or writing all parameters (4 seconds max). 

    They can also be turned off with the `Mute CAN TX` input, see [Device Settings](#device-0x0000).

### dingoPDM / dingoPDM-Max

dingoPDM-Max has 4 outputs, the bytes for outputs 5 to 8 are always `0`.

| Msg | CAN ID        | Always Sent    | Byte 0 | Byte 1 | Byte 2 | Byte 3 | Byte 4 | Byte 5 | Byte 6 | Byte 7 |
| :-: | :-----------: | :------------: | :----: | :----: | :----: | :----: | :----: | :----: | :----: | :----: |
| 0   | Base ID + 2   | :green_square: | DI     | DS     | TC     | TC     | BV     | BV     | BT     | BT     |
| 1   | Base ID + 3   | :green_square: | OC1    | OC1    | OC2    | OC2    | OC3    | OC3    | OC4    | OC4    |
| 2   | Base ID + 4   | :green_square: | OC5    | OC5    | OC6    | OC6    | OC7    | OC7    | OC8    | OC8    |
| 3   | Base ID + 5   | :green_square: | OS12   | OS34   | OS56   | OS78   | WO     | WSS    | FL     | 0      |
| 4   | Base ID + 6   | :green_square: | OR1    | OR2    | OR3    | OR4    | OR5    | OR6    | OR7    | OR8    |
| 5   | Base ID + 7   |                | CI     | CI     | CI     | CI     | VI     | VI     | VI     | VI     |
| 6   | Base ID + 8   |                | CNT1   | CNT2   | CNT3   | CNT4   | CON    | CON    | CON    | CON    |
| 7   | Base ID + 9   |                | CIV1   | CIV1   | CIV1   | CIV1   | CIV2   | CIV2   | CIV2   | CIV2   |
| ... | ...           |                |        |        |        |        |        |        |        |        |
| 22  | Base ID + 24  |                | CIV31  | CIV31  | CIV31  | CIV31  | CIV32  | CIV32  | CIV32  | CIV32  |
| 23  | Base ID + 25  |                | DC1    | DC2    | DC3    | DC4    | DC5    | DC6    | DC7    | DC8    |
| 24  | Base ID + 26  |                | K1B    | K1B    | K1B    | K2B    | K2B    | K2B    | 0      | 0      |
| 25  | Base ID + 27  |                | K1D1   | K1D1   | K1D2   | K1D2   | 0      | 0      | 0      | 0      |
| 26  | Base ID + 28  |                | K2D1   | K2D1   | K2D2   | K2D2   | 0      | 0      | 0      | 0      |
| 27  | Base ID + 29  |                | TO     | TR     | 0      | 0      | 0      | 0      | 0      | 0      |
| 28  | Base ID + 30  |                | TE1    | TE1    | TE2    | TE2    | TE3    | TE3    | TE4    | TE4    |
| 29  | Base ID + 31  |                | TE5    | TE5    | TE6    | TE6    | TE7    | TE7    | TE8    | TE8    |

- **Msg 0**
    - `DI` - Digital inputs
        - Bit 0 = Input 1
        - Bit 1 = Input 2
    - `DS` - Device state and type
        - Bits 0-3 = [Device state](./design.md#device-state)
            - 0 = `Run`
            - 1 = `Sleep`
            - 2 = `OverTemp`
            - 3 = `Error`
        - Bits 4-7 = Device type
            - 0 = dingoPDM
            - 1 = dingoPDM-Max
    - `TC` - Total current (A)
    - `BV` - Battery voltage (V × 10)
    - `BT` - Board temperature (°C × 10)
- **Msg 1, 2**
    - `OCn` - Output _n_ current (A)
- **Msg 3**
    - `OSxy` - Output states
        - Bits 0-3 = Output _x_
        - Bits 4-7 = Output _y_
            - 0 = `Off`
            - 1 = `On`
            - 2 = `Overcurrent`
            - 3 = `Fault`
    - `WO` - Wiper outputs
        - Bit 0 = Slow
        - Bit 1 = Fast
    - `WSS` - Wiper speed and state
        - Bits 0-3 = [Wiper speed](./wipers.md#speed-map)
        - Bits 4-7 = [Wiper state](./wipers.md#states)
    - `FL` - Flashers
        - Bit 0 to 3 = Flasher 1 to 4
- **Msg 4**
    - `ORn` - Output _n_ overcurrent count
- **Msg 5** - Sent when any CAN input or virtual input is enabled
    - `CI` - CAN input results, bit 0 = CAN input 1 ... bit 31 = CAN input 32
    - `VI` - Virtual input results, bit 32 = virtual input 1 ... bit 47 = virtual input 16
- **Msg 6** - Sent when any counter or condition is enabled
    - `CNTn` - Counter _n_ value
    - `CON` - Condition results, bit 32 = condition 1 ... bit 63 = condition 32
- **Msg 7 to 22** - Sent when either CAN input in the message is enabled
    - `CIVn` - CAN input _n_ value, 32 bits
    - Encoded using that CAN input's factor, offset and byte order (the raw value of the input signal)
- **Msg 23** - Sent when PWM is enabled on any output
    - `DCn` - Output _n_ duty cycle (%)
- **Msg 24** - Sent when any keypad is enabled
    - `K1B` - Keypad 1 buttons, bit 0 = button 1 ... bit 19 = button 20
    - `K2B` - Keypad 2 buttons, bit 24 = button 1 ... bit 43 = button 20
- **Msg 25, 26** - Sent when keypad 1 / keypad 2 is enabled
    - `KxDn` - Keypad _x_ dial _n_ value, 16 bits (2 dials per keypad)
- **Msg 27** - Sent when any timer is enabled
    - `TO` - Timer outputs, bit 0 = timer 1 ... bit 7 = timer 8
    - `TR` - Timers running (timing), bit 8 = timer 1 ... bit 15 = timer 8
- **Msg 28, 29** - Sent when any timer is enabled
    - `TEn` - Timer _n_ elapsed time, 16 bits, 10ms per bit (s × 100)
    - Held at the preset time once it is reached, `0` when the timer is not running

### CANBoard

See [CANBoard](../canboard/introduction.md#can-messages)

## Parameters

All settings are parameters, addressed by an `Index` and a `SubIndex`. 

Parameters are read and written on `Base ID + 1`. The device responds on `Base ID + 0`. All parameter frames are 8 bytes:

| Byte 0  | Byte 1     | Byte 2     | Byte 3    | Byte 4 | Byte 5 | Byte 6 | Byte 7 |
| :-----: | :--------: | :--------: | :-------: | :----: | :----: | :----: | :----: |
| Command | Index low  | Index high | SubIndex  | Value  | Value  | Value  | Value  |

- `Value` is 32 bits, little endian
    - `Float` values are sent as the IEEE-754 bit pattern
    - Signed values are sign-extended to 32 bits
    - `Bool` = `0` or `1`, `Enum` = the number of the enum value

### Commands

| Command | Name                     | Direction | Description |
| :-----: | ------------------------ | --------- | ----------- |
| 1       | `Read`                   | Both      | Read one parameter. Response has the current value |
| 2       | `Write`                  | Both      | Write one parameter. Response has the new value. No response if the value is out of range |
| 5       | `ReadParamNotFound`      | Response  | Index/SubIndex does not exist |
| 10      | `ReadAll`                | Both      | Read every parameter. Device responds with `ReadAll`, then one `ReadAllRsp` per parameter, then `ReadAllComplete` |
| 11      | `ReadAllRsp`             | Response  | One parameter value |
| 12      | `ReadAllComplete`        | Response  | Bytes 1-2 = number of parameters sent, bytes 4-7 = CRC32 of the values |
| 13      | `ReadAllModified`        | Both      | Same as `ReadAll`, only parameters that are not the default value |
| 20      | `WriteAll`               | Both      | Start writing every parameter |
| 21      | `WriteAllVal`            | Request   | One parameter value |
| 22      | `WriteAllComplete`       | Both      | Request: bytes 1-2 = number of parameters sent, bytes 4-7 = CRC32 of the values. Response: bytes 1-2 = number received, byte 3 = `1` if applied, bytes 4-7 = CRC32 |
| 23      | `WriteAllModified`       | Both      | Same as `WriteAll`, only parameters that are not the default value. Parameters not sent are set to default |
| 24      | `WriteAllMissing`        | Response  | Sent after a failed `WriteAll`, one per missing parameter |
| 25      | `WriteAllParamNotFound`  | Response  | `WriteAllVal` Index/SubIndex does not exist |
| 26      | `WriteAllOutOfRange`     | Response  | `WriteAllVal` value is out of range |
| 27      | `WriteAllMissingDone`    | Response  | End of the missing list. Bytes 1-2 = count, `0xFFFF` = too many to list |
| 34      | `CheckCrc`               | Request   | Request the CRC32 of all parameter values |
| 35      | `CheckCrcRsp`            | Response  | Bytes 4-7 = CRC32 of all parameter values |

!!! Note
    `WriteAll` and `WriteAllModified` are only applied if the parameter count and CRC32 match. Otherwise none of the values are applied.

    dingoConfig uses `CheckCrc` to compare its copy of the configuration with the device, a mismatch shows `Config Mismatch`.

!!! Warning
    Written values are used immediately, but are lost on a power cycle. Send a [Burn](#burn) to save them.

### Parameter Indexes

`n` is the instance number starting at `0` (Output 1 = `0x1000`, Output 2 = `0x1001`, ...).

| Index            | Function |
| ---------------- | -------- |
| `0x0000`         | [Device](#device-0x0000) |
| `0x1000 + n`     | [Outputs](#outputs-0x1000) |
| `0x1200 + n`     | [Digital inputs](#digital-inputs-0x1200) |
| `0x1300 + n`     | [CAN inputs](#can-inputs-0x1300) |
| `0x1400 + n`     | [Virtual inputs](#virtual-inputs-0x1400) |
| `0x1500 + n`     | [Conditions](#conditions-0x1500) |
| `0x1600 + n`     | [Counters](#counters-0x1600) |
| `0x1700 + n`     | [Flashers](#flashers-0x1700) |
| `0x1800`         | [Starter disable](#starter-disable-0x1800) |
| `0x1900`         | [Wiper](#wiper-0x1900) |
| `0x1A00 + n`     | [Timers](#timers-0x1a00) |
| `0x2000 + n`     | [CAN outputs](#can-outputs-0x2000) |
| `0x2100 + n`     | [Digital outputs](#digital-outputs-0x2100) |
| `0x2200 + n`     | [Analog inputs](#analog-inputs-0x2200) |
| `0x3000 + n`     | [Keypads](#keypads-0x3000) |
| `0x3100 + (keypad × 32) + button` | [Keypad buttons](#keypad-buttons-0x3100) |
| `0x3200 + (keypad × 4) + dial`    | [Keypad dials](#keypad-dials-0x3200) |

Variable inputs (`Var`) are [variable map](./variablemap.md) indexes.

#### Device `0x0000`

| Sub | Name                 | Type   | Default | Range | Notes |
| :-: | -------------------- | ------ | ------- | ----- | ----- |
| 0   | Base ID              | UInt16 | `0x0DE` | 0 - 0x7FF | Used right away |
| 1   | CAN bitrate          | Enum   | 1 | 0 - 4 | 0 = 1000K, 1 = 500K, 2 = 250K, 3 = 125K, 4 = 100K. Used after a restart |
| 2   | Sleep enabled        | Bool   | 0 | | See [Sleep](./sleep.md) |
| 3   | CAN filter enabled   | Bool   | 0 | | Hardware filter, only accept IDs the device uses |
| 4   | Disable digital input wake | Bool | 0 | | |
| 5   | Disable CAN wake     | Bool   | 0 | | |
| 6   | Connect USB to CAN   | Bool   | 1 | | Bridge USB and CAN, the USB port works as a USB to CAN adapter |
| 7   | Mute CAN TX input    | Var    | 0 | | Stop cyclic messages while the variable is true |
| 8   | Force sleep input    | Var    | 0 | | Go to sleep while the variable is true |

#### Outputs `0x1000`

| Sub | Name                  | Type   | Default | Range | Notes |
| :-: | --------------------- | ------ | ------- | ----- | ----- |
| 0   | Enabled               | Bool   | 0 | | |
| 1   | Input                 | Var    | 0 | | Output is on while the variable is not `0` |
| 2   | Current limit         | Float  | 20 | 0 - 100 | A |
| 3   | Inrush current limit  | Float  | 50 | 0 - 100 | A |
| 4   | Inrush time           | UInt16 | 1000 | 0 - 10000 | ms |
| 5   | Reset mode            | Enum   | 0 | 0 - 2 | 0 = None, 1 = Count, 2 = Endless |
| 6   | Reset time            | UInt16 | 1000 | 0 - 60000 | ms |
| 7   | Reset limit           | UInt8  | 3 | 0 - 20 | |
| 8   | PWM enabled           | Bool   | 0 | | |
| 9   | Soft start            | Bool   | 0 | | |
| 10  | Variable duty cycle   | Bool   | 0 | | |
| 11  | Duty cycle input      | Var    | 0 | | |
| 12  | Fixed duty cycle      | UInt8  | 100 | 0 - 100 | % |
| 13  | PWM frequency         | UInt16 | 100 | 0 - 400 | Hz |
| 14  | Soft start ramp time  | UInt16 | 0 | 0 - 10000 | ms |
| 15  | Duty cycle denominator | UInt16 | 100 | 1 - 5000 | Duty cycle = input / denominator |
| 16  | Minimum duty cycle    | UInt16 | 0 | 0 - 100 | % |
| 17  | Primary output        | Int8   | -1 | -1 - | Output to follow, `-1` = none. See [Paired Outputs](../dingoconfig/dingopdm.md#paired-outputs) |

#### Digital Inputs `0x1200`

| Sub | Name           | Type   | Default | Range | Notes |
| :-: | -------------- | ------ | ------- | ----- | ----- |
| 0   | Enabled        | Bool   | 0 | | |
| 1   | Mode           | Enum   | 0 | 0 - 1 | 0 = Momentary, 1 = Latching |
| 2   | Invert         | Bool   | 0 | | |
| 3   | Debounce time  | UInt16 | 20 | 0 - 1000 | ms |
| 4   | Pull           | Enum   | 0 | 0 - 2 | 0 = None, 1 = Pullup, 2 = Pulldown |

#### CAN Inputs `0x1300`

| Sub | Name            | Type   | Default | Range | Notes |
| :-: | --------------- | ------ | ------- | ----- | ----- |
| 0   | Enabled         | Bool   | 0 | | |
| 1   | Timeout enabled | Bool   | 0 | | |
| 2   | Timeout         | UInt16 | 1000 | 0 - 60000 | ms |
| 3   | ID type         | UInt8  | 0 | 0 - 1 | 0 = Standard, 1 = Extended |
| 4   | ID              | UInt32 | 0 | 0 - 0x1FFFFFFF | |
| 5   | Start bit       | UInt8  | 0 | 0 - 63 | |
| 6   | Bit length      | UInt8  | 8 | 1 - 32 | |
| 7   | Factor          | Float  | 1 | | |
| 8   | Offset          | Float  | 0 | | |
| 9   | Byte order      | Enum   | 0 | 0 - 1 | 0 = Little Endian, 1 = Big Endian |
| 10  | Signed          | Bool   | 0 | | |
| 11  | Operator        | Enum   | 0 | 0 - 7 | See [operators](#can-inputs) |
| 12  | Operand         | Float  | 0 | | |
| 13  | Mode            | Enum   | 0 | 0 - 1 | 0 = Momentary, 1 = Latching |

#### Virtual Inputs `0x1400`

| Sub | Name         | Type   | Default | Range | Notes |
| :-: | ------------ | ------ | ------- | ----- | ----- |
| 0   | Enabled      | Bool   | 0 | | |
| 1   | Not 0        | Bool   | 0 | | Invert variable 0 |
| 2   | Variable 0   | Var    | 0 | | |
| 3   | Condition 0  | Enum   | 0 | 0 - 2 | 0 = And, 1 = Or, 2 = Nor |
| 4   | Not 1        | Bool   | 0 | | Invert variable 1 |
| 5   | Variable 1   | Var    | 0 | | |
| 6   | Condition 1  | Enum   | 0 | 0 - 2 | 0 = And, 1 = Or, 2 = Nor |
| 7   | Not 2        | Bool   | 0 | | Invert variable 2 |
| 8   | Variable 2   | Var    | 0 | | `0` = only use variables 0 and 1 |
| 9   | Mode         | Enum   | 0 | 0 - 1 | 0 = Momentary, 1 = Latching |

#### Conditions `0x1500`

| Sub | Name      | Type   | Default | Range | Notes |
| :-: | --------- | ------ | ------- | ----- | ----- |
| 0   | Enabled   | Bool   | 0 | | |
| 1   | Input     | Var    | 0 | | |
| 2   | Operator  | Enum   | 0 | 0 - 7 | See [operators](#can-inputs) |
| 3   | Argument  | Float  | 0 | | |

#### Counters `0x1600`

| Sub | Name            | Type   | Default | Range | Notes |
| :-: | --------------- | ------ | ------- | ----- | ----- |
| 0   | Enabled         | Bool   | 0 | | |
| 1   | Increment input | Var    | 0 | | |
| 2   | Decrement input | Var    | 0 | | |
| 3   | Reset input     | Var    | 0 | | |
| 4   | Minimum         | UInt8  | 0 | 0 - 255 | |
| 5   | Maximum         | UInt8  | 10 | 0 - 255 | |
| 6   | Increment edge  | Enum   | 0 | 0 - 2 | 0 = Rising, 1 = Falling, 2 = Both |
| 7   | Decrement edge  | Enum   | 0 | 0 - 2 | |
| 8   | Reset edge      | Enum   | 0 | 0 - 2 | |
| 9   | Wrap around     | Bool   | 0 | | |
| 10  | Hold to reset   | Bool   | 0 | | |
| 11  | Hold reset time | UInt16 | 2000 | 0 - 10000 | ms |

#### Flashers `0x1700`

| Sub | Name       | Type   | Default | Range | Notes |
| :-: | ---------- | ------ | ------- | ----- | ----- |
| 0   | Enabled    | Bool   | 0 | | |
| 1   | Input      | Var    | 0 | | |
| 2   | On time    | UInt16 | 500 | 0 - 5000 | ms |
| 3   | Off time   | UInt16 | 500 | 0 - 5000 | ms |
| 4   | Single     | Bool   | 0 | | One flash per activation of the input |

#### Starter Disable `0x1800`

| Sub | Name               | Type | Default | Notes |
| :-: | ------------------ | ---- | ------- | ----- |
| 0   | Enabled            | Bool | 0 | |
| 1   | Input              | Var  | 0 | |
| 2+n | Disable output _n+1_ | Bool | 0 | Turn this output off while the input is true |

#### Wiper `0x1900`

| Sub     | Name              | Type   | Default | Range | Notes |
| :-----: | ----------------- | ------ | ------- | ----- | ----- |
| 0       | Enabled           | Bool   | 0 | | |
| 1       | Mode              | Enum   | 0 | 0 - 2 | 0 = DigIn, 1 = IntIn, 2 = MixIn |
| 2       | Slow input        | Var    | 0 | | |
| 3       | Fast input        | Var    | 0 | | |
| 4       | Intermittent input | Var   | 0 | | |
| 5       | On input          | Var    | 0 | | |
| 6       | Speed input       | Var    | 0 | | |
| 7       | Park input        | Var    | 0 | | |
| 8       | Park stop level   | Bool   | 0 | | 0 = stop when park input is low, 1 = high |
| 9       | Swipe input       | Var    | 0 | | |
| 10      | Wash input        | Var    | 0 | | |
| 11      | Wash wipe cycles  | UInt8  | 3 | 0 - 10 | |
| 12 - 19 | Speed map 0 - 7   | Enum   | | 0 - 8 | See [Speed Map](./wipers.md#speed-map) |
| 20 - 25 | Intermittent time 1 - 6 | UInt16 | 1000 - 6000 | 0 - 30000 | ms |

#### Timers `0x1A00`

| Sub | Name        | Type   | Default | Range | Notes |
| :-: | ----------- | ------ | ------- | ----- | ----- |
| 0   | Enabled     | Bool   | 0 | | |
| 1   | Input       | Var    | 0 | | |
| 2   | Reset input | Var    | 0 | | Level, timer is cleared while on |
| 3   | Mode        | Enum   | 0 | 0 - 3 | 0 = On delay, 1 = Off delay, 2 = Pulse (retriggerable), 3 = Pulse (one shot) |
| 4   | Time        | UInt32 | 1000 | 0 - 600000 | ms |

#### CAN Outputs `0x2000`

| Sub | Name       | Type   | Default | Range | Notes |
| :-: | ---------- | ------ | ------- | ----- | ----- |
| 0   | Enabled    | Bool   | 0 | | |
| 1   | Input      | Var    | 0 | | |
| 2   | ID type    | UInt8  | 0 | 0 - 1 | 0 = Standard, 1 = Extended |
| 3   | ID         | UInt32 | 0 | 0 - 0x1FFFFFFF | |
| 4   | Start bit  | UInt8  | 0 | 0 - 63 | |
| 5   | Bit length | UInt8  | 8 | 1 - 32 | |
| 6   | Factor     | Float  | 1 | | |
| 7   | Offset     | Float  | 0 | | |
| 8   | Byte order | Enum   | 0 | 0 - 1 | 0 = Little Endian, 1 = Big Endian |
| 9   | Signed     | Bool   | 0 | | |
| 10  | Interval   | UInt16 | 1000 | 0 - 60000 | ms |

#### Digital Outputs `0x2100`

CANBoard only.

| Sub | Name    | Type | Default |
| :-: | ------- | ---- | ------- |
| 0   | Enabled | Bool | 0 |
| 1   | Input   | Var  | 0 |

#### Analog Inputs `0x2200`

CANBoard only.

| Sub | Name                  | Type   | Default | Range | Notes |
| :-: | --------------------- | ------ | ------- | ----- | ----- |
| 0   | Enabled               | Bool   | 0 | | |
| 1   | Switch enabled        | Bool   | 0 | | Use the input as a digital switch |
| 2   | Switch mode           | Enum   | 0 | 0 - 1 | 0 = Momentary, 1 = Latching |
| 3   | Switch invert         | Bool   | 0 | | |
| 4   | Switch threshold      | UInt16 | 2000 | 0 - 5000 | mV, switch is on above this voltage |
| 5   | Rotary enabled        | Bool   | 0 | | Use the input as a rotary switch |
| 6   | Rotary invert         | Bool   | 0 | | |
| 7   | Rotary offset         | Float  | 0 | | mV at position 0 |
| 8   | Rotary step           | Float  | 100 | | mV per position |
| 9   | Rotary max position   | Float  | 10 | | |

#### Keypads `0x3000`

| Sub | Name                     | Type   | Default | Range | Notes |
| :-: | ------------------------ | ------ | ------- | ----- | ----- |
| 0   | Enabled                  | Bool   | 0 | | |
| 1   | Node ID                  | UInt8  | 0 | 0 - 127 | CANopen node ID |
| 2   | Timeout enabled          | Bool   | 0 | | |
| 3   | Timeout                  | UInt16 | 0 | 0 - 60000 | ms |
| 4   | Model                    | Enum   | 6 | | See [Keypad models](./keypad.md#models) |
| 5   | Backlight brightness     | UInt8  | 63 | 0 - 63 | |
| 6   | Dim backlight brightness | UInt8  | 32 | 0 - 63 | |
| 7   | Backlight color          | UInt8  | 0 | 0 - 9 | See [Backlight colors](./keypad.md#backlight-colors) |
| 8   | Dimming input            | Var    | 0 | | Use dim brightness while true |
| 9   | Button brightness        | UInt8  | 63 | 0 - 63 | |
| 10  | Dim button brightness    | UInt8  | 32 | 0 - 63 | |

#### Keypad Buttons `0x3100`

| Sub     | Name                     | Type   | Default | Range | Notes |
| :-----: | ------------------------ | ------ | ------- | ----- | ----- |
| 0       | Enabled                  | Bool   | 0 | | |
| 1       | Mode                     | Enum   | 0 | 0 - 1 | 0 = Momentary, 1 = Latching |
| 2 - 5   | Color 0 - 3              | UInt8  | 0 | 0 - 7 | See [Button colors](./keypad.md#button-colors) |
| 6       | Fault color              | UInt8  | 0 | 0 - 7 | |
| 7 - 10  | Value variable 0 - 3     | Var    | 0 | | |
| 11      | Fault variable           | Var    | 0 | | |
| 12 - 15 | Blink 0 - 3              | Bool   | 0 | | |
| 16      | Fault blink              | Bool   | 0 | | |
| 17 - 20 | Blink color 0 - 3        | UInt8  | 0 | 0 - 7 | |
| 21      | Fault blink color        | UInt8  | 0 | 0 - 7 | |

#### Keypad Dials `0x3200`

| Sub | Name       | Type  | Default | Range |
| :-: | ---------- | ----- | ------- | ----- |
| 0   | Enabled    | Bool  | 0 | |
| 1   | Min count  | UInt8 | 0 | 0 - 16 |
| 2   | Max count  | UInt8 | 16 | 0 - 16 |
| 3   | LED offset | UInt8 | 0 | 0 - 16 |

## Requests

Requests are sent on `Base ID + 1` and answered on `Base ID + 0`. All request frames are 8 bytes, unused bytes are `0`.

### Burn

Saves the configuration to memory (FRAM on dingoPDM, flash on CANBoard). Without a Burn, changes are lost on a power cycle.

| Type     | Byte 0 | Byte 1 | Byte 2 | Byte 3 | Byte 4 |
| :------: | :----: | :----: | :----: | :----: | :----: |
| Request  | 30     | 1      | 3      | 8      |        |
| Response | 30     | 1      | 3      | 8      | Result (1 = success, 0 = fail) |

!!! Tip
    This is an easy way to test new settings. Try new settings without a Burn, then cycle power to go back to the saved settings.

### Version

| Type     | Byte 0 | Byte 4 | Byte 5 | Byte 6     | Byte 7    |
| :------: | :----: | :----: | :----: | :--------: | :-------: |
| Request  | 31     |        |        |            |           |
| Response | 31     | Major  | Minor  | Build high | Build low |

### Sleep

Puts the device to sleep immediately, even if the [sleep conditions](./sleep.md) are not met. All outputs are turned off.

| Type     | Byte 0 | Byte 1 | Byte 2 | Byte 3 | Byte 4 | Byte 5 |
| :------: | :----: | :----: | :----: | :----: | :----: | :----: |
| Request  | 32     | `Q`    | `U`    | `I`    | `T`    |        |
| Response | 32     | `Q`    | `U`    | `I`    | `T`    | 1      |

### Bootloader

Jumps to the STM32 USB bootloader for [firmware updating](./updating.md). No response. Devices with USB only.

| Type     | Byte 0 | Byte 1 | Byte 2 | Byte 3 | Byte 4 | Byte 5 |
| :------: | :----: | :----: | :----: | :----: | :----: | :----: |
| Request  | 33     | `B`    | `O`    | `O`    | `T`    | `L`    |

### Restart

Resets the device. Needed after changing the bitrate. Settings that have not been burned are lost.

| Type     | Byte 0 | Byte 1 | Byte 2 | Byte 3 | Byte 4 | Byte 5 | Byte 6 |
| :------: | :----: | :----: | :----: | :----: | :----: | :----: | :----: |
| Request  | 36     | `R`    | `E`    | `S`    | `E`    | `T`    |        |
| Response | 36     | `R`    | `E`    | `S`    | `E`    | `T`    | 1      |

### Wake Up

Any CAN message wakes the device from sleep (unless CAN wake is disabled). There is no special wake up message.

## Messages

The device sends info, warning and error messages on `Base ID + 0` when something changes state. Each message is sent once when its condition becomes true.

| Byte 0 | Byte 1 | Bytes 2-3 | Bytes 4-5 | Bytes 6-7 |
| :----: | :----: | :-------: | :-------: | :-------: |
| Type   | Source | Data 1    | Data 2    | Data 3    |

- Type
    - `F` (70) = Info
    - `R` (82) = Warning
    - `E` (69) = Error

| Source | Name             | Type          | Data 1                    | Data 2          | Description |
| :----: | ---------------- | ------------- | ------------------------- | --------------- | ----------- |
| 1      | State Run        | Info          |                           |                 | Device entered `Run` |
| 2      | State Sleep      | Info          |                           |                 | Device entered `Sleep` |
| 3      | State Overtemp   | Error         | Board temp (°C × 10)      |                 | Device entered `OverTemp` |
| 4      | State Error      | Error         | Board temp (°C × 10), or [error number](./errors.md) | Total current (A × 10) | Device entered `Error` |
| 5      | Overcurrent      | Warning/Error | Output number (0 = output 1) | Current (A)  | Warning = overcurrent, Error = output fault |
| 6      | Voltage          | Warning       | Battery voltage (V × 10)  |                 | Battery under 10V or over 16V |
| 7      | CAN              |               |                           |                 | |
| 8      | USB              |               |                           |                 | |
| 9      | Overtemp         |               |                           |                 | |
| 10     | Config           | Error         | [Error number](./errors.md) |               | Configuration error |
| 11     | FRAM             |               |                           |                 | |
| 12     | Analog           |               |                           |                 | |
| 13     | I2C              |               |                           |                 | |
| 14     | Temp Sensor      |               |                           |                 | |
| 15     | USB Connection   |               |                           |                 | |
| 16     | Init             | Error         | [Error number](./errors.md) |               | Error during startup |

### Errors

Error messages are sent when a non-recoverable issue occurs. The only way to reset is to cycle power on the device. 

For fatal errors, `Data 1` is the [error number](./errors.md#error-numbers). 
