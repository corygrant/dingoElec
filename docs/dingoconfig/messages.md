# Messages

The `Messages` page (System Log) shows everything dingoConfig logs: adapter connections, device reads/writes/burns, timeouts, and the [info, warning and error messages](../dingofw/can.md#messages) sent by devices.

| Item        | Description |
| ----------- | ----------- |
| Level       | Show only `Info`, `Warning`, `Error` or `All` |
| Show Debug  | Also show `Debug` messages |
| Source      | Show only messages from one source |
| Log to File | Save messages to a CSV file in the `logs` folder next to the dingoConfig program |

Errors are also shown as a pop-up at the bottom of the screen.

## Device Messages

Device warning and error messages show the device name, Base ID, [source](../dingofw/can.md#messages) and the 3 data values:

```
Front ID: 222, Src: OverCurrent 0 25 0
```

This example is an overcurrent on output 1 (`0` = output 1) at 25A.

Common sources:

- `OverCurrent`: output number and current. Warning = overcurrent, Error = output fault
- `BatteryVoltage`: battery voltage (V × 10) under 10V or over 16V
- `StateOvertemp`, `StateError`: device state changes
- `Init`, `Config`: fatal errors, data 1 is the [error number](../dingofw/errors.md)

## Common Messages

| Message | Meaning |
| ------- | ------- |
| `Firmware needs to be updated` | The device firmware is older than this version of dingoConfig supports. [Update the firmware](../dingofw/updating.md) |
| `Message retry` / `Message failed after 2 retries` | The device didn't answer. Check the Base ID, bitrate and wiring |
| `Burn Failed` | The device couldn't save the settings |
