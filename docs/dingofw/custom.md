# Adding Custom Board Support to dingoFW

## Overview

This guide walks you through adding support for a custom PDM board to dingoFW. By following these steps, you'll build the firmware to run on your custom hardware while reusing existing functionality.

## Hardware

### Pins

The pins you need depend on which Profets (switching devices) you're planning to use.

#### BTS7002-1EPP

| Pin Type       | Count | Name  | Purpose                               |
| -------------- | ----- | ----- | ------------------------------------- |
| Analog Input   | 1     | `IS`  | Current feedback                      |
| Digital Output | 1     | `IN`  | Drive transistor ON/OFF (PWM-enabled) |
| Digital Output | 1     | `DEN` | Enable current sensing                |

#### BTS7008-2EPA

| Pin Type       | Count | Name   | Purpose                                   |
| -------------- | ----- | ------ | ----------------------------------------- |
| Analog Input   | 1     | `IS`   | Current feedback                          |
| Digital Output | 2     | `IN`   | Drive transistor ON/OFF, one per output   |
| Digital Output | 1     | `DSEL` | Select which output's current is measured |
| Digital Output | 1     | `DEN`  | Enable current sensing                    |

!!! Note
    The BTS7008-2EPA has two outputs that share one current feedback pin, `DSEL` selects which output is measured. The BTS7002-1EPP and BTS70012-1ESP have one output and a dedicated feedback pin (same pins as the BTS7002-1EPP).

## Firmware

dingoFW is modular. Adding a new board is mostly done inside a new `boards/<your_board>/` folder. Follow these steps in order:

### Overview

- [ ] Copy and rename an existing board folder
- [ ] Rename the `.ld` linker script
- [ ] Update `board.mk`
- [ ] Define pins in `board.h`
- [ ] Set the device type and counts in `port.h`
- [ ] Declare outputs and inputs in `hw_devices.cpp`
- [ ] Add PWM configuration in `port_pwm.h`
- [ ] Enable timers in `cfg/mcuconf.h` (if needed)
- [ ] List the parameters in `params.h`
- [ ] Write the cyclic messages in `msg.cpp`
- [ ] Add the board to `.vscode/tasks.json` and `.github/workflows`
- [ ] Add the device to dingoConfig

!!! Tip
    Start from the board closest to yours. `dingopdm_v7` has both single and dual channel Profets, `canboard_v2` has analog inputs.

---

### Files

#### Linker Script (`.ld`)

**Location:** `boards/your_board/`

Rename the `.ld` file to match the board folder name exactly (`your_board.ld`). The Makefile uses `boards/$(BOARD)/$(BOARD).ld`.

---

#### Board Configuration (`board.mk`)

**Location:** `boards/your_board/`

???+ Example

    ```makefile
    # List of all the board related files
    BOARDSRC = ./boards/your_board/board.c

    # Required include directories
    BOARDINC = ./boards/your_board
    ```

---

#### Pin Definitions (`board.h`)

**Location:** `boards/your_board/`

This file initializes all MCU pins. Name the output lines like the existing boards (`LINE_PF1_IN`, `LINE_PF1_DEN`, `LINE_PF3_4_DSEL`, ...), the other board files use these names.

!!! Note
    Refer to ChibiOS documentation on `board.h` configuration

???+ abstract "`IN` Pin Configuration"

    ```c
    Mode     = OUTPUT
    Otype    = PUSHPULL
    Ospeed   = HIGH
    Pupdr    = PULLDOWN
    Odr      = LOW
    ```

???+ abstract "`DEN` Pin Configuration"

    ```c
    Mode     = OUTPUT
    Otype    = PUSHPULL
    Ospeed   = HIGH
    Pupdr    = PULLDOWN
    Odr      = LOW
    ```

???+ abstract "`DSEL` Pin Configuration"

    ```c
    Mode     = OUTPUT
    Otype    = PUSHPULL
    Ospeed   = HIGH
    Pupdr    = PULLDOWN
    Odr      = LOW
    ```

???+ abstract "`IS` Pin Configuration"

    ```c
    Mode     = ANALOG
    Otype    = PUSHPULL
    Ospeed   = LOW
    Pupdr    = FLOATING
    Odr      = LOW
    ```

---

#### Device Type and Counts (`port.h`)

**Location:** `boards/your_board/`

???+ Example

    ```c
    #define PDM_TYPE 4              // Unique device type, 0-3 are used (3 = CANBoard in dingoConfig)

    #define HAS_SE_LEDS TRUE        // Status/Error LEDs
    #define HAS_USB TRUE
    #define HAS_I2C TRUE
    #define HAS_EXT_TEMP_SENSOR TRUE
    #define HAS_EXT_MEMORY TRUE     // FRAM, FALSE = internal flash
    #define HAS_BATT_VOLT_SENSE TRUE
    #define CAN_SLEEP TRUE

    #define NUM_OUTPUTS 8           // Profet outputs
    #define NUM_DIG_OUTPUTS 0
    #define NUM_DIG_INPUTS 2
    #define NUM_ANALOG_INPUTS 0
    #define NUM_VIRT_INPUTS 16
    #define NUM_CAN_INPUTS 32
    #define NUM_CAN_OUTPUTS 32
    #define NUM_FLASHERS 4
    #define NUM_COUNTERS 4
    #define NUM_CONDITIONS 32
    #define NUM_KEYPADS 2
    #define HAS_WIPERS TRUE
    #define HAS_STARTER_DISABLE TRUE

    #define NUM_TX_MSGS 27          // Number of cyclic messages in msg.cpp
    #define DEFAULT_BASE_ID 0x0DE
    ```

`VAR_MAP_SYS_VARS` must match the system variables the board has (None, Always On, State, plus temperature and battery voltage if present). `VAR_MAP_SIZE` is calculated from the counts. The firmware stops with [error 15](./errors.md) if the [variable map](./variablemap.md) size doesn't match.

`port.cpp` has the ADC setup, CAN bitrate timings and the `GetBattVolt()`/`GetTemperature()` functions. Update them to match your hardware.

---

#### Output Declarations (`hw_devices.cpp`)

**Location:** `boards/your_board/`

Declare each output with its number, Profet model, pins, current sense ADC channel and PWM timer.

- Profet models: `BTS7002_1EPP`, `BTS7008_2EPA_CH1`, `BTS7008_2EPA_CH2`, `BTS70012_1ESP`
- Each output needs its own PWM timer (`&PWMD3`, `&PWMD4`, ...)
- Dual channel Profets share `DEN`, `DSEL` and `IS` between the `CH1` and `CH2` outputs
- Unused pins use `LINE_UNUSED`

???+ Example

    ```cpp
    Profet pf[NUM_OUTPUTS] = {
        Profet(1, ProfetModel::BTS7002_1EPP, LINE_PF1_IN, LINE_PF1_DEN, LINE_UNUSED, AnalogChannel::IS1, &PWMD3, &pwm3Cfg, PwmChannel::Ch1),
        Profet(3, ProfetModel::BTS7008_2EPA_CH1, LINE_PF3_IN, LINE_PF3_4_DEN, LINE_PF3_4_DSEL, AnalogChannel::IS3_4, &PWMD5, &pwm5Cfg, PwmChannel::Ch1),
        Profet(4, ProfetModel::BTS7008_2EPA_CH2, LINE_PF4_IN, LINE_PF3_4_DEN, LINE_PF3_4_DSEL, AnalogChannel::IS3_4, &PWMD9, &pwm9Cfg, PwmChannel::Ch1),
        // ...
    };

    Digital_Input digIn[NUM_DIG_INPUTS] = {
        Digital_Input(LINE_DI1),
        Digital_Input(LINE_DI2)};
    ```

---

#### PWM Configuration (`port_pwm.h`)

**Location:** `boards/your_board/`

The `IN` pins are switched by the PWM timer callbacks, so any GPIO can be used for `IN`.

1. Add a period callback for each output, this sets the `IN` pin high
2. Add a duty cycle callback for each output, this sets the `IN` pin low
3. Add a `PWMConfig` for each timer that uses those callbacks

---

#### Timer Configuration (`cfg/mcuconf.h`)

**Location:** `boards/your_board/cfg/`

- Enable new timer peripherals **only if** your PWM configuration uses timers not already enabled
- If reusing timers from the reference board, no changes needed

---

#### Parameters (`params.h`)

**Location:** `boards/your_board/`

List one parameter macro per function instance, for example 8 outputs = `OUTPUT_PARAMS(0)` to `OUTPUT_PARAMS(7)`. The counts must match `port.h`. 

---

#### Cyclic Messages (`msg.cpp`, `msg.h`)

**Location:** `boards/your_board/`

One `TxMsgN()` function per [cyclic message](./can.md#cyclic-messages), listed in the `TxMsgs` array in `msg.h`. Message _n_ is sent on `Base ID + 2 + n`. `NUM_TX_MSGS` in `port.h` must match the number of messages.

---

#### Build Setup

- `.vscode/tasks.json`: add the board to the `boardType` options
- `.github/workflows/`: add the board to the release builds

---

#### dingoConfig

dingoConfig needs to know about the new device type:

- `web/Definitions/device-definitions.json`: add an entry with the `deviceType` (same as `PDM_TYPE`), default ID and function counts
- `web/Definitions/<your_board>-signals.json`: describe the cyclic messages (copy `dingopdm-signals.json` and edit)

---

## Build and Flash

Once all files are configured:

- **Build the firmware**
   ```bash
   make clean
   make BOARD=your_board
   ```

- **Flash to your STM32**
    - Follow the [firmware updating](./updating.md) instructions

---

## Validation Checklist

Before testing your custom board:

- [ ] All pin definitions in `board.h` match your schematic
- [ ] `NUM_OUTPUTS` and `NUM_DIG_INPUTS` match your hardware
- [ ] `PDM_TYPE` is unique and matches the dingoConfig device definition
- [ ] `params.h` lists the same number of each function as `port.h`
- [ ] `NUM_TX_MSGS` matches the `TxMsgs` array in `msg.h`
- [ ] All PWM timer assignments are unique (no conflicts)
- [ ] `.ld` linker script is renamed correctly
- [ ] Firmware builds without errors: `make BOARD=your_board`

---

## Common Issues & Troubleshooting

| Issue                          | Cause                                       | Solution                                                 |
| ------------------------------ | ------------------------------------------- | -------------------------------------------------------- |
| **Linker errors**              | `.ld` file not renamed or path wrong        | Verify `.ld` filename matches board folder name exactly  |
| **Error LED blinks 15**        | Variable map size mismatch                  | Check `VAR_MAP_SYS_VARS` and the counts in `port.h`      |
| **PWM conflicts**              | Timer assigned to multiple outputs          | Review `hw_devices.cpp` and use unique timers            |
| **Build fails: unknown board** | Board folder name and `BOARD=` don't match  | Use the folder name: `make BOARD=your_board`             |
| **Outputs not responding**     | Wrong pin configuration                     | Double-check `board.h` pin modes                         |
| **dingoConfig shows wrong data** | Signals file doesn't match `msg.cpp`      | Update `<your_board>-signals.json`                       |

---

## Reference: File Structure

```
dingoFW/
├── Makefile
├── boards/
│   ├── dingopdm_v7/
│   │   ├── cfg/
│   │   │   ├── chconf.h
│   │   │   ├── halconf.h
│   │   │   └── mcuconf.h
│   │   ├── board.c
│   │   ├── board.h
│   │   ├── board.mk
│   │   ├── dingopdm_v7.ld
│   │   ├── hw_devices.cpp / .h
│   │   ├── msg.cpp / .h
│   │   ├── params.h
│   │   ├── port.cpp / .h
│   │   └── port_pwm.h
│   └── your_board/          ← Copy here
├── comms/
├── core/
└── functions/
```

---

## Next Steps

After building and flashing:

- Verify all outputs turn on/off correctly
- Monitor current feedback (`IS`) in dingoConfig
- Test PWM duty cycle control on `IN` pins
- Validate multiplexing on `DSEL` (BTS7008-2EPA only)

For questions, refer to the [dingoFW GitHub repository](https://github.com/corygrant/dingoFW) or ask on Discord.

!!! Note
 
    Thanks to **jabbarider** for this write up