[![Donate](https://img.shields.io/badge/Donate-PayPal-blue.svg)](https://www.paypal.com/donate/?hosted_button_id=HDE8YCVY9NR2L)
[![Discord](https://img.shields.io/discord/1243358184266010667?label=discord)](https://discord.com/invite/TxnPYQkVu3)

## What is this?

dingoElectronics is a project to build open source automotive electronics. 

The project includes:

- [dingoPDM](../dingopdm/introduction.md) and [dingoPDM-Max](../dingopdm/introduction.md)
    - Power distribution modules
- [dingoFW](../dingofw/introduction.md)
    - Firmware used for all of the devices
- [dingoConfig](../dingoconfig/introduction.md)
    - Software to configure the devices
- [CANBoard](../canboard/introduction.md)
    - Small CAN enabled I/O board
- [LINBoard](../linboard/introduction.md)
    - LIN to CAN gateway device
- [Custom Devices](../custom/introduction.md)
    - Custom devices created by others that utilize dingoFW and dingoConfig

## Quick Start

1. Download and run [dingoConfig](../dingoconfig/introduction.md#installation)
2. Check for a newer firmware release and [update the firmware](../dingofw/updating.md) if needed
3. Wire the device, see [Connections](../dingopdm/connections.md)
4. Connect with USB or a USB to CAN [adapter](../dingoconfig/adapters.md)
5. [Add the device](../dingoconfig/devices.md#adding-a-device) in dingoConfig (default Base ID `0x0DE`)
6. Set up the outputs, inputs and logic, see [dingoPDM Configuration](../dingoconfig/dingopdm.md)
7. `Write`, then `Burn` to save the settings on the device
8. Save a [config file](../dingoconfig/configfiles.md) as a backup

## User Locations

:flag_us: :flag_de: :flag_fr: :flag_gb: :flag_pt: :flag_au: :flag_kn: :flag_jp: :flag_bh: :flag_it: :flag_fi: :flag_dk:

```vegalite 
{
    "schema-url": "assets/charts/countries_chart.json"
}
```

```vegalite 
{
    "schema-url": "assets/charts/states_chart.json"
}
```

## Resources

### GitHub
dingoElectronics is split between multiple GitHub repos.

The latest releases can be found on the Releases page for each repo:

- Hardware: 
    - [dingoPDM](https://github.com/corygrant/dingoPDM/releases) 
    - [dingoPDM-Max](https://github.com/corygrant/dingoPDM-Max/releases)
    - [CANBoard](https://github.com/corygrant/CANBoard/releases)
    - [LINBoard](https://github.com/corygrant/LINBoard/releases)
- Firmware: 
    - [dingoFW](https://github.com/corygrant/dingoFW/releases)
- Software: 
    - [dingoConfig](https://github.com/corygrant/dingoConfig/releases)

!!! Note
    Devices are shipped with the latest firmware release installed at the time of shipping, but new firmware is released frequently. 

    Check the Releases page for a new release when receiving the device and follow the [Firmware Updating](../dingofw/updating.md) instructions. 

### Store

Built and tested dingoPDMs and other devices can be purchased through the dingoElectronics Store. 

All PDMs and devices are assembled and tested in Virginia :flag_us:

[dingoElectronics Store](https://dingo-electronics.square.site/)

### Discord

See invite link at the bottom right of the page

!!! Note
    If the link is expired, send a message on the store page 

### Facebook

See link at the bottom right of the page

## Goals

- [X] Learn more about PDMs and high DC current devices
- [X] Learn about developing firmware for a more complex STM32 based project
- [X] Create a low cost device that my friends and I can use in our project cars
- [X] Layout the board to fit simple case designs
    * [X] 3D printed top shell and flat aluminum heatsinks (no machining)
- [X] Use low cost 1oz/0.5oz 4 layer PCBs (low cost 2oz/1oz now available from JLCPCB)
- [X] Share my work with others for reference, inspiration or collaboration. 
- [X] Document the project in a professional way to make it easy to use and access
- [X] Develop an easy to use configuration PC software
- [X] Offer fully built devices for sale

!!! info
    If this project helps you in any way, I'd appreciate a message!

## Contributing 

See the [contributing guide](https://github.com/corygrant/dingoElec/blob/main/CONTRIBUTING.md).

## Disclaimer

!!! warning
    Please note that these products have been designed by a hobbyist, not a professional. 
    They are intended for off-road and testing use only. 
    Users should operate the products at their own discretion and risk. 
    The designer explicitly disclaims any responsibility for damage or injury that may result from the use of these products.