[![Donate](https://img.shields.io/badge/Donate-PayPal-blue.svg)](https://www.paypal.com/donate/?hosted_button_id=HDE8YCVY9NR2L)
[![Discord](https://img.shields.io/discord/1243358184266010667?label=discord)](https://discord.com/invite/TxnPYQkVu3)
[![GitHub Release](https://img.shields.io/github/v/release/corygrant/dingoPDM?include_prereleases&display_name=tag)](https://github.com/corygrant/dingoPDM/releases)
[![GitHub last commit](https://img.shields.io/github/last-commit/corygrant/dingoPDM)](https://github.com/corygrant/dingoPDM/commits/master/)
[![GitHub Issues or Pull Requests](https://img.shields.io/github/issues/corygrant/dingoPDM)](https://github.com/corygrant/dingoPDM/issues)

## What Is It?

dingoPDM is an automotive `Power Distribution Module`. 

PDMs can be used in place of fuses, circuit breakers and relays, including flasher and wiper relays. 

Each output channel of the PDM:
 
- Measures the active current and reacts by turning the output off if the current exceeds the limit
- Automatically resets after a cool down period a maximum number of times
- Can be turned on/off using various input sources
- Can be on constantly or with Pulse Width Modulation(PWM)

This makes PDMs a very flexible solution when designing the power system in any automotive application. 

## Features

A key feature of any PDM is flexible configuration to match the target application. 

To meet these needs, dingoPDM includes configurable:

- Outputs
- Digital inputs
- CAN inputs
- Virtual logic inputs
- Counters
- Logic conditions
- Wipers
- Flashers
- Starter disable

All settings on the dingoPDM can be controller by the custom Windows application [DingoConfigurator](../software/introduction.md) 

dingoPDM also includes:

- Cyclic CAN status messages (TX)
- Pulse Width Modulation (PWM)
    - Configurable base frequency
    - Fixed or variable duty cycle
- CAN keypad support (work in progress)
    - Blink Marine
    - Grayhill
- Battery voltage measurement
- Internal temperature sensor
- Status LEDs

## Configuration

The dingoPDM configuration can be accessed using:

- USB-C connected directly to the device, one at a time
- A CAN connection to one or many dingoPDMs at once using a USB to CAN adapter such as [USB2CAN](https://github.com/corygrant/USB2CAN_HW)

Configuration reading/writing and status monitoring are all done using the custom built Windows application [DingoConfigurator](../software/introduction.md) 

## Wiring Example

![ExampleWiring](../images/dingoPDMExampleWiringWhite.svg#only-dark){ .off-glb }
![ExampleWiring](../images/dingoPDMExampleWiringBlack.svg#only-light){ .off-glb }

## Changing CAN ID

The CAN Base ID default value is 2000. 

To change this, modify the value in the [DeviceTree](../software/devicetree.md) and press `Update` and then `Burn` to save the setting. 

On the next power up, the CAN Base ID will be updated. 

![Full1](images/Full1.jpg)

![Full2](images/Full2.jpg)

![PCB1](images/PCB1.jpg)

![PCB2](images/PCB2.jpg)