# Sleep

Sleep is used on dingoPDM and dingoPDM-Max to reduce current draw when the vehicle is off. CANBoard does not sleep.

Sleep is disabled by default. Turn it on with `Sleep Enabled` on the dingoConfig `Configuration` tab, `Device` section.

## Entering Sleep

The device goes to sleep when **all** of these are true for `30 seconds`:

- Sleep is enabled
- No outputs on
- No USB connected
- No CAN messages received

The device also goes to sleep right away when:

- A [Sleep request](./can.md#sleep) is received (dingoConfig `Sleep` button)
- The `Force Sleep Input` variable is true

!!! Warning
    A Sleep request or `Force Sleep Input` turns off all outputs, even if they are on.

## Wake

Sleep is exited by any of these:

- Any CAN traffic (can be disabled with `Disable CAN Wake`)
- A digital input changing state (can be disabled with `Disable Digital Input Wake`)
- USB cable connected (always on)

!!! Tip
    If the device keeps waking up from other traffic on the CAN bus, enable `Disable CAN Wake` and wire an ignition signal to a digital input to wake it.

## Current Draw

!!! Tip
    To further reduce power consumption, the power LED can be disabled by cutting the solder bridge near the LED

!!! Note "v7.2 Hardware"
    v7.2 hardware does not have the power LED solder bridge or CAN disable pin connected.

    To further reduce consumption, the LED or resistor must be de-soldered. 

    A jumper wire can be added between E3 test pad and the CAN transceiver pin 8 (Rs)

Test results

- :green_square: : Enabled
- :red_square: : Disabled
- :yellow_square: : Requires modification

| State            | LED            | CAN Transceiver | Current (@12V) | v7.2 HW         |
| ---------------- | -------------- | --------------  | -------------- | --------------- |
| Run (no outputs) | :green_square: | :green_square:  | 40-50mA        | :green_square:  |
| Run (no outputs) | :red_square:   | :green_square:  | 35-45mA        | :yellow_square: |
| Sleep            | :green_square: | :green_square:  | 17mA           | :green_square:  |
| Sleep            | :red_square:   | :green_square:  | 13mA           | :yellow_square: |
| Sleep            | :green_square: | :red_square:    | 8mA            | :yellow_square: |
| Sleep            | :red_square:   | :red_square:    | 6mA            | :yellow_square: |
