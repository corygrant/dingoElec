# dingoPDM Configuration

The device `Configuration` tab in dingoConfig has a section for each function. This page explains each setting. dingoPDM-Max uses the same settings.

!!! Note
    Every function that takes an input uses a [variable](../dingofw/variablemap.md). In dingoConfig, click the input to pick the variable from a list.

!!! Tip
    Functions that are not enabled are not processed, which saves processing time on the device. Leave unused functions disabled.

!!! Warning
    After changing settings, press `Write` to send them to the device and `Burn` to save them.

## Device

| Setting              | Description |
| -------------------- | ----------- |
| Base ID              | First CAN ID of the device, see [CAN IDs](../dingofw/can.md#ids). Change it with `Modify Device`, see [Changing the Base ID](../dingopdm/introduction.md#changing-the-can-base-id) |
| Bitrate              | CAN bitrate. Used after a `Reset` |
| Sleep Enabled        | Allow the device to [sleep](../dingofw/sleep.md) |
| CAN Filters Enabled  | Hardware CAN filter, the device only receives the IDs it uses (its config ID, enabled CAN inputs, keypads). Reduces processing on busy CAN buses |
| Disable Digital Input Wake | Digital inputs don't wake the device from sleep |
| Disable CAN Wake     | CAN messages don't wake the device from sleep |
| Connect USB to CAN   | Use the USB port as a USB to CAN adapter for the whole CAN bus, see [Adapters](./adapters.md#usb) |
| Keypad 1/2 Enabled   | Use a keypad, see [Keypads](./keypads.md) |
| Mute CAN TX Input    | Stop sending the cyclic status messages while this variable is on |
| Force Sleep Input    | Go to sleep while this variable is on |

## Outputs

Each output turns on when its input variable is on (not `0`) and the output isn't disabled by [Starter Disable](#starter-disable).

| Setting        | Description |
| -------------- | ----------- |
| Enabled        | Output is used. Disabled outputs are always off |
| Name           | Name shown in dingoConfig |
| Input          | Variable that turns the output on |
| Current Limit  | Overcurrent limit (A) |
| Inrush Limit   | Overcurrent limit during the inrush time (A) |
| Inrush Time    | Time after turning on that the inrush limit is used (ms) |
| Reset Mode     | What to do after an overcurrent, see below |
| Reset Time     | Time to wait after an overcurrent before turning back on (ms) |
| Reset Limit    | Number of overcurrents before the output faults (`Count` mode) |
| Primary Output | Pair this output with another output, see [Paired Outputs](#paired-outputs) |

### Output States

| State         | Description |
| ------------- | ----------- |
| `Off`         | Output off |
| `On`          | Output on |
| `Overcurrent` | Current went over the limit, the output is off and waiting for the reset time |
| `Fault`       | Output is off and stays off until power is cycled |

### Reset Mode

| Mode      | After an overcurrent |
| --------- | -------------------- |
| `None`    | Output faults right away |
| `Count`   | Output turns back on after the reset time, up to the reset limit. Then it faults |
| `Endless` | Output turns back on after the reset time, forever |

The overcurrent count resets when the output's input turns off.

!!! Warning
    A `Fault` can only be cleared by cycling power. A fault is also set if the Profet reports a hardware fault (short circuit, over temperature).

### Inrush

Motors, lamps and capacitive loads draw much more current when they first turn on. For the inrush time after the output turns on, the inrush limit is used instead of the current limit.

!!! Example
    A fuel pump runs at 8A but draws 20A for the first 0.5s.

    - Current Limit = `10`
    - Inrush Limit = `25`
    - Inrush Time = `500`

### Paired Outputs

Two outputs can be wired together to share a high current load. Set `Primary Output` on the second (follower) output to the first output.

- The follower turns on/off and PWMs with the primary
- The primary reports the total current of both outputs, and the current limit applies to the total
- A fault on either output faults both

!!! Warning
    Only pair outputs that are wired to the same load. The follower must be `Enabled`, its own input and current settings are not used.

## Outputs PWM

Outputs can be switched with Pulse Width Modulation (PWM) to dim lights or control motor speed.

| Setting               | Description |
| --------------------- | ----------- |
| PWM Enabled           | Use PWM on this output |
| Frequency             | PWM frequency (1 to 400 Hz) |
| Variable Duty Cycle   | Duty cycle comes from a variable instead of the fixed duty cycle |
| Fixed Duty Cycle      | Duty cycle when not variable (%) |
| Duty Cycle Input      | Variable for the duty cycle (usually a CAN input value or counter) |
| Duty Cycle Denominator | Duty cycle (%) = input / denominator. Use `1` if the input is already 0-100% |
| Min Duty Cycle        | Lowest duty cycle (%) |
| Soft Start            | Ramp the duty cycle up when the output turns on |
| Soft Start Ramp Time  | Time to ramp from the minimum to the target duty cycle (ms) |

!!! Note
    The output's current is measured during the on part of the PWM cycle. At high frequency and low duty cycle the on time can be too short to measure, the minimum duty cycle for good current readings is about 2% at 100Hz and 7% at 400Hz.

## Digital Inputs

| Setting       | Description |
| ------------- | ----------- |
| Enabled       | Input is used |
| Mode          | `Momentary`: follows the input. `Latching`: toggles on each press |
| Invert        | Invert the input |
| Debounce Time | Time the input must be stable before it changes (ms) |
| Pull          | `None`, `Pullup` (switch to ground) or `Pulldown` (switch to 12V) |

## CAN Inputs

CAN inputs read a signal from any CAN message on the bus and compare it to a value.

See [CAN Inputs](../dingofw/can.md#can-inputs) for each setting and examples.

!!! Tip
    Press the `+` button on a CAN input to fill it from a signal of another device in dingoConfig, for example a [DBC Device](./dbcdevice.md) or a CANBoard.

## CAN Outputs

CAN outputs send any variable out on the CAN bus, for example to show output states or currents on a dash.

See [CAN Outputs](../dingofw/can.md#can-outputs) for each setting.

## Virtual Inputs

Virtual inputs combine 2 or 3 variables with logic.

```
Result = (Var0 [Cond0] Var1) [Cond1] Var2
```

| Setting     | Description |
| ----------- | ----------- |
| Enabled     | Input is used |
| Not 0/1/2   | Invert the variable before the logic |
| Var 0/1/2   | Variables to combine. Set `Var 2` to `None` to only use 2 variables |
| Cond 0/1    | `And`, `Or` or `Nor` |
| Mode        | `Momentary`: follows the result. `Latching`: toggles each time the result turns on |

!!! Example
    Turn on the high beams only when the headlights are on and the high beam switch is on.

    - Var 0 = `Output 3 On` (headlights)
    - Cond 0 = `And`
    - Var 1 = `Digital In 2` (high beam switch)
    - Var 2 = `None`
    - Output 4 (high beams) Input = `Virtual In 1`

## Conditions

Conditions compare a variable to a number. The result is `1` when true.

| Setting  | Description |
| -------- | ----------- |
| Enabled  | Condition is used |
| Input    | Variable to compare |
| Operator | `Equal`, `Not Equal`, `Greater Than`, `Less Than`, `Greater Than or Equal`, `Less Than or Equal`, `Bitwise And`, `Bitwise Nand` |
| Argument | Number to compare against |

!!! Example
    Turn on the radiator fan when coolant temperature (CAN In Val 1) is over 95°C.

    - Input = `CAN In 1 Value`
    - Operator = `Greater Than`
    - Argument = `95`
    - Output 2 (fan) Input = `Condition 1`

## Counters

Counters count up and down on input edges. Useful for multi-position switches made from a button, or wiper speed.

| Setting         | Description |
| --------------- | ----------- |
| Enabled         | Counter is used |
| Increment Input | Variable that counts up |
| Decrement Input | Variable that counts down |
| Reset Input     | Variable that resets the counter to the minimum |
| Increment/Decrement/Reset Edge | `Rising`, `Falling` or `Both` |
| Min / Max       | Counter range (0 to 255) |
| Wrap Around     | Go from max to min (and min to max) instead of stopping |
| Hold To Reset   | Holding the increment or decrement input resets the counter |
| Hold Reset Time | Time to hold for a reset (ms) |

## Flashers

Flashers turn on and off while their input is on, for turn signals and hazards.

| Setting  | Description |
| -------- | ----------- |
| Enabled  | Flasher is used |
| Input    | Variable that turns the flasher on |
| On Time  | Time on (ms) |
| Off Time | Time off (ms) |
| Single   | Flash once each time the input turns on |

Use the flasher variable (`Flasher n`) as the output input.

## Timers

Timers turn their variable (`Timer n`) on or off after a set time. Useful for courtesy lights, delayed fans or a timed shutdown.

| Setting     | Description |
| ----------- | ----------- |
| Enabled     | Timer is used |
| Input       | Variable that starts the timer |
| Reset Input | Variable that clears the timer. While it is on the timer is off and stays cleared |
| Mode        | See below |
| Time        | Preset time (0 to 600000 ms) |

| Mode                  | Timer variable |
| --------------------- | -------------- |
| On delay              | Turns on once the input has been on for the time. Turns off as soon as the input turns off |
| Off delay             | Turns on with the input. Stays on for the time after the input turns off |
| Pulse (retriggerable) | Each time the input turns on, turns on for the time. Turning the input on again restarts the time |
| Pulse (one shot)      | Each time the input turns on, turns on for the time. The input is ignored until the pulse ends |

!!! Note
    An on delay timer starts timing even if its input is already on at power up or when the reset input turns off. The pulse modes only start when the input turns on, so an input that is already on at power up does not start a pulse.

The Timers grid also shows whether each timer is running and its elapsed time.

!!! Example
    Keep the interior light on for 30 seconds after the door closes.

    - Input = `Digital In 1` (door switch)
    - Mode = `Off delay`
    - Time = `30000`
    - Output 5 (interior light) Input = `Timer 1`

## Wiper

See [Wipers](../dingofw/wipers.md) for the wiper modes, inputs and speed map.

To control a wiper motor directly from dingoPDM a WiperModule is required. 
The WiperModule uses 3 relays to properly control the slow, fast and brake functions of a wiper motor. 
The internal connections are made based on the slow input (which also supplies all power to the motor) and the fast input. 
The ground and park signals are passed through the module. 

Information about the WiperModule can be found here: [WiperModule](https://github.com/corygrant/WiperModule)

![WiperModuleConnections](../images/WiperModuleStatesWhite.svg#only-dark){ .off-glb }
![WiperModuleConnections](../images/WiperModuleStatesBlack.svg#only-light){ .off-glb }

## Starter Disable

Turns off selected outputs while the engine is cranking, for example to drop accessory loads.

| Setting  | Description |
| -------- | ----------- |
| Enabled  | Starter disable is used |
| Input    | Variable that is on while cranking |
| Output 1-8 | Outputs to turn off while the input is on |

## Keypads

See [Keypads](./keypads.md)
