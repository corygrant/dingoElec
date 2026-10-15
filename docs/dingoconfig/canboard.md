# CANBoard Configuration

CANBoard is added and configured like a dingoPDM, see [Devices](./devices.md). The default Base ID is `0x640` (1600).

CANBoard has no USB. Connect with a USB to CAN [adapter](./adapters.md) (SLCAN, PCAN or SocketCAN), or through the [USB port of a dingoPDM](./adapters.md#usb) on the same bus.

The `Configuration` tab has these sections:

| Section         | Description |
| --------------- | ----------- |
| Device          | Base ID, bitrate, CAN filters |
| Analog Inputs   | Enable, switch mode, see [Analog Inputs](../canboard/introduction.md#analog-inputs) |
| Rotary Switch   | Rotary switch settings for each analog input |
| Digital Inputs  | Same as [dingoPDM](./dingopdm.md#digital-inputs) |
| Digital Outputs | Enable and input variable |
| CAN Inputs      | Same as [dingoPDM](./dingopdm.md#can-inputs) |
| CAN Outputs     | Same as [dingoPDM](./dingopdm.md#can-outputs) |
| Virtual Inputs  | Same as [dingoPDM](./dingopdm.md#virtual-inputs) |
| Conditions      | Same as [dingoPDM](./dingopdm.md#conditions) |
| Counters        | Same as [dingoPDM](./dingopdm.md#counters) |
| Flashers        | Same as [dingoPDM](./dingopdm.md#flashers) |

!!! Tip
    A CANBoard's signals (switches, rotary positions, analog voltages) can be copied into a dingoPDM CAN input with the `+` button on the CAN input. Add the CANBoard to dingoConfig first.

See [CANBoard](../canboard/introduction.md) for the hardware and CAN messages.
