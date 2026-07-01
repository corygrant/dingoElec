## What is this?

dingoElectronics is a project to build open source automotive electronics. 

The project includes:

- [dingoPDM](../dingopdm/introduction.md) and [dingoPDM-Max](../dingopdm/introduction.md)
    - Power distributions modules 
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

    Check the Releases page for a new release when receiving the device and follow the [Firwmare Updating](https://corygrant.github.io/dingoElec/dingofw/updating/) instructions. 

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

## Project Status

- :green_square: : Complete
- :yellow_square: : In Progess
- :red_square: : Not Started

| Recent Open Items                   | Status          |
| ----------------------------------- | --------------- |
| Finish case design                  | :green_square:  |
| Test thermal performance            | :green_square:  |
| Support USB C to C cables           | :green_square:  |
| Develop dingoPDM-Max                | :green_square:  |
| Finish documentation                | :yellow_square: |
| Add device sleep                    | :green_square:  |
| Add verbose error handling          | :green_square:  |
| Add keypad support                  | :green_square:  |
| Add PWM output support              | :green_square:  |
| Switch-less bootloader              | :green_square:  |
| Add soft HMI buttons                | :green_square:  |
| Finish plots in software            | :yellow_square: |
| Move to ChibiOS                     | :green_square:  |
| Move to web based UI                | :green_square:  |
| Move all devices to common firmware | :green_square:  |

## Documentation Status

???+ Note

    Any help with the documentation is always welcome. 
    
    If you're familiar with git, submit a PR with your updates. 
    
    If not, you can message me the updates/additions on Discord and I can make the changes. 

    Markdown preferred, but not required. 


- :yellow_square: : Needs to be updated
- :red_square: : Does not exist

| Section                             | Status          | Notes                      |
| ----------------------------------- | --------------- | -------------------------- |
| [Custom](../custom/introduction.md) | :yellow_square: | Custom device instructions |

## Disclaimer

!!! warning
    Please note that these products have been designed by a hobbyist, not a professional. 
    They are intended for off-road and testing use only. 
    Users should operate the products at their own discretion and risk. 
    The designer explicitly disclaims any responsibility for damage or injury that may result from the use of these products.