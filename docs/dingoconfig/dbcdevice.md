# DBC Device

A DBC Device shows the signals of any CAN device that has a DBC file, for example an ECU, a dash or a sensor. It is monitor only, dingoConfig doesn't send anything to it.

## Setup

1. Go to `Devices`, press `Create New Device` and select `DBC Device`
2. Open the device and go to the `Configuration` tab
3. Press `Open DBC File` and pick the `.dbc` file

The file browser starts in the [working directory](./configfiles.md#location).

The signals are saved in the [config file](./configfiles.md), the DBC file isn't needed after it's loaded.

## Custom Signals

Signals can also be added by hand under `Custom Signal`:

| Setting   | Description |
| --------- | ----------- |
| Name      | Signal name |
| ID        | CAN ID |
| Start Bit | First bit of the signal |
| Length    | Length in bits |
| Factor    | Value = (raw × Factor) + Offset |
| Offset    | Value = (raw × Factor) + Offset |
| Signed    | Signal is signed |
| Min / Max | Signal range |
| Unit      | Unit shown in dingoConfig |

## Using the Signals

- The `Dashboard` tab shows the live value of each signal
- Signals can be plotted on the `Plot` tab and the [Plot page](./devices.md#plot-page)
- A signal can be copied into a dingoPDM [CAN input](./dingopdm.md#can-inputs) with the `+` button, so the ID, start bit, length, factor and offset don't have to be typed in
