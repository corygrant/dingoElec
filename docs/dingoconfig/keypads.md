# Keypads

Keypads show up in dingoConfig in two ways.

## Keypads on a dingoPDM

A dingoPDM can be the master of up to 2 CANopen keypads. The PDM reads the buttons and sets the LEDs, no other device is needed.

1. Add the keypad as a [keypad device](#keypad-devices) on the `Devices` page, with its model and CANopen node ID
2. Open the PDM, go to the `Configuration` tab and turn on `Keypad 1 Enabled` in the `Device` section
3. A `Keypad 1` tab appears. Select the keypad device under `Assign new keypad`
4. Set up the backlight, buttons and dials
5. `Write` and `Burn`

!!! Note
    If the keypad's node ID is changed, select the keypad again under `Assign new keypad`.

Each button and dial is then a [variable](../dingofw/variablemap.md) that can turn on outputs or be used in logic.

| Section          | Description |
| ---------------- | ----------- |
| Device Settings  | Enable, node ID, model, timeout, backlight and brightness |
| Button LED Vars  | Mode and the variables that pick each button's LED color |
| Button LED Colors | LED color for each variable and the fault |
| Button LEDs      | Blink settings |
| Dial LEDs        | Dial range and LED offset |

See [dingoFW Keypads](../dingofw/keypad.md) for supported models and every setting.

## Keypad Devices

A keypad can also be added as its own device on the `Devices` page (`Blink Marine Keypad` or `Grayhill Keypad`). Use this to:

- See the button, dial and analog input states and LED colors on the `Dashboard` tab
- Set button icons and modes on the `Settings` tab
- Simulate a keypad: turn on `Simulated` on the `Settings` tab, then click the buttons and dials on the `Dashboard` tab. dingoConfig sends the keypad CAN messages, so a PDM keypad setup can be tested without a real keypad

The device ID for a keypad device is the keypad's CANopen node ID.

| Brand        | Models |
| ------------ | ------ |
| Blink Marine | PKP-1100, PKP-1200, PKP-1500, PKP-1600, PKP-2200, PKP-2300, PKP-2400, PKP-2500, PKP-2600, PKP-3500, PKP-3500-MT |
| Grayhill     | 3Kx06, 3Kx08, 3Kx12, 3Kx15, 3Kx20 |
