# Devices

The `Devices` page lists every device in the open [config file](./configfiles.md). Each device also shows in the left menu, green when its messages are being received.

## Adding a Device

1. Go to `Devices`
2. Press `Create New Device`
3. Select the `Device Type`
4. Enter a `Device Name`
5. Enter the device's `Base ID`
6. For keypads, select the `Model`

| Device Type          | Default Base ID |
| -------------------- | --------------- |
| dingoPDM             | `0x0DE` (222)   |
| dingoPDM-Max         | `0x0DE` (222)   |
| CANBoard             | `0x640` (1600)  |
| Blink Marine Keypad  | CANopen node ID |
| Grayhill Keypad      | CANopen node ID |
| DBC Device           | Not used        |

!!! Warning
    The Base ID entered in dingoConfig must match the Base ID set on the device, or dingoConfig won't see the device. 

The name and Base ID can be changed later with `Modify Device`. Changing the Base ID with `Modify Device` also changes it on a connected device, press `Burn` to save it. See [Changing the Base ID](../dingopdm/introduction.md#changing-the-can-base-id).

## Device Page

Click a device in the left menu to open it.

### Buttons

| Button       | Description |
| ------------ | ----------- |
| `Read`       | Read the settings from the device that are not default |
| `Write`      | Write the settings that are not default. Every other setting is set to default |
| `Burn`       | Save the settings on the device, so they are kept after a power cycle |
| `Sleep`      | Put the device to sleep, all outputs turn off |
| `Wakeup`     | Wake the device from sleep |
| `Bootloader` | Jump to the USB bootloader for [firmware updates](../dingofw/updating.md) |
| `Read All`   | Read every setting from the device |
| `Write All`  | Write every setting to the device |
| `Reset`      | Restart the device. Needed after changing the bitrate |

The `Read` button flashes when a device is added or connects, as a reminder to read the settings.

!!! Warning
    `Write` and `Write All` change the settings the device is using right away, but they are lost on a power cycle until you press `Burn`.

`Config Mismatch` is shown when the settings in dingoConfig don't match the device. `Read` or `Write` to fix it.

`Connected` / `Disconnected` shows whether messages from the device are being received.

### Tabs

| Tab             | Description |
| --------------- | ----------- |
| `Dashboard`     | Live status: battery voltage, current, temperature, state, firmware version, outputs, inputs, logic. Small plots show the last 30 seconds |
| `Configuration` | All settings, see [dingoPDM Configuration](./dingopdm.md) and [CANBoard](./canboard.md) |
| `Flow`          | Visual editor that shows how inputs, logic and outputs are connected. Add functions and connect them by dragging |
| `Plot`          | Plot any of the device's values |

## Plot Page

The `Plot` page in the left menu records up to 8 signals from any device for up to 10 minutes.

- `Signals` picks the signals to record
- `Record`, `Pause` and `Stop` control the recording
- Signals with the same unit share a plot
- Drag to zoom, double click to follow live data again
