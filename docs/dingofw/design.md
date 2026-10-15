# Design

## Device State

dingoFW uses a simple state machine to control overtemperature, sleep and error behavior. 

``` mermaid
stateDiagram
    [*] --> Run : Power On
    Run --> OverTemp : Temp > 55°C
    OverTemp --> Run : Temp < 55°C
    Run --> Sleep : Sleep conditions met
    Sleep --> Run : Wake input
    Run --> Error : Fatal error
    OverTemp --> Error : Temp > 80°C
    Error --> [*] : Power Cycle Required
```

See [Sleep](./sleep.md) for information on the sleep timeout and wake

See [Errors](./errors.md) for information on possible fatal errors

See [LEDs](./leds.md) for possible status and error LED states

- `Run`
    - Default state
- `Sleep`
    - All outputs off
    - Low power state
- `OverTemp`
    - Board temperature over the warning limit (`55°C`)
    - Warning message sent
    - Outputs remain on
- `Error`
    - All outputs off
    - Fatal error state, power cycle required to recover
    - Board temperature over the critical limit (`80°C`) also goes to `Error`

## Main Loop

The device thread runs every `2ms` and updates everything in this order, so outputs always act on this cycle's inputs and logic:

1. Received CAN messages (CAN inputs, keypads)
2. Digital inputs, analog inputs, CAN input timeouts
3. Virtual inputs
4. Wiper, starter disable
5. Flashers, counters, conditions
6. Keypads
7. Outputs, digital outputs, CAN outputs

Other threads:

| Thread         | Rate        | Job |
| -------------- | ----------- | --- |
| Slow           | 250ms       | Battery voltage and temperature sensor |
| CAN Cyclic Tx  | 100ms       | [Cyclic status messages](./can.md#cyclic-messages) |
| CAN Tx / Rx    | As needed   | Send and receive CAN frames |
| USB Tx / Rx    | As needed   | Send and receive USB frames |
| Param          | As needed   | [Parameter](./can.md#parameters) reads/writes and requests |
| Keypad         | 100ms       | Keypad LED and backlight messages |

## Files

dingoFW files are split between ChibiOS, OpenOCD and dingoFW files.

### ChibiOS

- `ChibiOS/`
    - ChibiOS repository (git submodule)
- `boards/<board>/cfg/`
    - ChibiOS configuration (`chconf.h`, `halconf.h`, `mcuconf.h`)
- `boards/<board>/board.c`, `board.h`, `board.mk`
    - ChibiOS board files, pin setup

### OpenOCD

- `interface/`
    - ST-Link debug interface
- `target/`
    - STM32F3/F4 debug configuration

### dingoFW

| Folder              | Contents |
| ------------------- | -------- |
| `boards/<board>/`   | Board specific code: pins, output/input declarations, cyclic messages, parameter list |
| `boards/cortex-m3/` | STM32F3 utilities (CANBoard) |
| `boards/cortex-m4/` | STM32F4 utilities (stop mode, bootloader jump) |
| `comms/`            | CAN, USB, mailboxes, info messages, requests |
| `core/`             | Device state, configuration, parameters, sleep, LEDs, errors |
| `functions/`        | Outputs, inputs, logic, wipers, keypads |
| `hardware/`         | FRAM (`mb85rc`) and temperature sensor (`mcp9808`) drivers |
| `utils/`            | CRC and DBC signal encode/decode |
| `dbc/`              | DBC files and the DBC builder |

#### Core

| File                     | Description |
| ------------------------ | ----------- |
| `main.cpp`               | Start ChibiOS and the device |
| `core/device_config.h`   | Firmware version, timing, temperature and voltage limits |
| `core/device`            | Device thread, state machine, variable map |
| `core/enums.h`           | All enums |
| `core/config`            | Configuration struct, read/write memory |
| `core/config_handler`    | Apply parameter changes to the functions |
| `core/param_defs.h`      | Parameter definitions (index, subindex, type, default, min, max) |
| `core/param_registry`    | Parameter table lookup, read, write |
| `core/param_protocol`    | Parameter CAN protocol |
| `core/sleep`             | Sleep conditions and wake sources |
| `core/status`            | Getters used by the cyclic messages |
| `core/led`               | Status/Error LEDs |
| `core/error`             | Fatal error handling |

#### Comms

| File                   | Description |
| ---------------------- | ----------- |
| `comms/can`            | CAN Tx/Rx threads, hardware filters |
| `comms/usb`            | USB CDC (SLCAN format) Tx/Rx |
| `comms/mailbox`        | CAN/USB Tx/Rx buffers |
| `comms/request_msg`    | Burn, sleep, bootloader, restart, version requests |
| `comms/infomsg`        | Info/warning/error messages |

#### Functions

| File                     | Description |
| ------------------------ | ----------- |
| `analog_input`           | Analog inputs, rotary switch and switch modes |
| `can_input`              | CAN input decoding and logic |
| `can_outputs`            | CAN output encoding and sending |
| `condition`              | Conditions |
| `counter`                | Counters |
| `digital_input`          | Digital inputs, debounce |
| `digital_output`         | Digital outputs |
| `edge`                   | Rising/falling edge detection |
| `flasher`                | Flashers |
| `input`                  | Momentary/latching logic |
| `profet`                 | Output control, current measurement, overcurrent, paired outputs |
| `pwm`                    | Output PWM and soft start |
| `starter`                | Starter disable |
| `virtual_input`          | Virtual inputs |
| `neopixels`              | Addressable LEDs |
| `keypad/`                | Blink Marine and Grayhill keypads |
| `wiper/`                 | Wiper modes |
