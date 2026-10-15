# Errors

## Error State

The device enters the error state when a non-recoverable issue occurs. All outputs are turned off.

An [error message](./can.md#messages) is sent over CAN and/or USB and the [Error LED](./leds.md#e-error) blinks the error number. 

!!! Warning
    The only way to reset from an error is to cycle power on the device. 

## Error Numbers

| Error # | Name               | Description |
| :-----: | ------------------ | ----------- |
| 1       | Watchdog           | Watchdog reset |
| 2       | Message Queue      | CAN/USB message queue error |
| 3       | Task               | Thread error |
| 4       | Configuration      | Could not write the default configuration |
| 5       | FRAM               | FRAM not responding |
| 6       | ADC                | ADC failed to start |
| 7       | Temperature Sensor | Temperature sensor failed to start |
| 8       | USB                | USB failed to start |
| 9       | CAN                | CAN failed to start |
| 10      | CRC                | CRC error |
| 11      | I2C                | I2C failed to start |
| 12      | RCC                | Clock error |
| 13      | Temperature        | Board temperature over the critical limit (80°C) |
| 14      | PWM                | PWM error |
| 15      | Variable Map       | Variable map size does not match (firmware build error) |

## Output Faults

Output faults are not device errors. A faulted output is turned off and stays off until power is cycled, but the rest of the device keeps running. See [Outputs](../dingoconfig/dingopdm.md#outputs).
