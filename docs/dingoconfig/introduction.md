[![Donate](https://img.shields.io/badge/Donate-PayPal-blue.svg)](https://www.paypal.com/donate/?hosted_button_id=HDE8YCVY9NR2L)
[![Discord](https://img.shields.io/discord/1243358184266010667?label=discord)](https://discord.com/invite/TxnPYQkVu3)
[![GitHub Release](https://img.shields.io/github/v/release/corygrant/dingoConfig?include_prereleases&display_name=tag)](https://github.com/corygrant/dingoConfig/releases)
[![GitHub last commit](https://img.shields.io/github/last-commit/corygrant/dingoConfig)](https://github.com/corygrant/dingoConfig/commits/master/)
[![GitHub Issues or Pull Requests](https://img.shields.io/github/issues/corygrant/dingoConfig)](https://github.com/corygrant/dingoConfig/issues)

!!! warning
    DingoConfigurator has been replaced by [dingoConfig](https://github.com/corygrant/dingoConfig/releases). Documentation will be updated. 

dingoConfig is a cross-platform application designed to configure and monitor dingoElectronics devices and other automotive electronics.


![PDM Main](../images/dingoConfig_Main.png){ width="800" }

## Download

The latest version can be downloaded here: [dingoConfig](https://github.com/corygrant/dingoConfig/releases)

## Installation

!!!Info
    First install [STM32CubeProgrammer](https://www.st.com/en/development-tools/stm32cubeprog.html) 

    Download the `.zip` from the link above, unzip and run `dingoConfig` ('dingoConfig.exe' on Windows)

## Supported Devices

dingoConfig currently supports these devices:

| Manufacturer      | Device                                                    |
| :---------------- | :-------------------------------------------------------- |
| dingoElectronics  | [dingoPDM](https://github.com/corygrant/dingoPDM)         |
| dingoElectronics  | [dingoPDM-Max](https://github.com/corygrant/dingoPDM-Max) |
| dingoElectronics  | [CANBoard](https://github.com/corygrant/CANBoard)         |
| PT-Motorsports AU | [PT-DPDM]()                                               |


## Supported CAN Interfaces

dingoConfig currently supports these CAN interfaces, in addition to direct USB connection.

| Manufacturer | Device Type                                                      |
| :----------- | :--------------------------------------------------------------- |
| Any          | [SLCAN](https://)                                                |
| Any          | [SocketCAN](https://) (Linux only)                               |
| PEAK-System  | [PCAN-USB](https://www.peak-system.com/PCAN-USB.199.0.html?&L=1) |

## Technology

dingoConfig is built using:

| Name                      | Function             |
| :------------------------ | :------------------- |
| [C# .NET](https://)       | Backend              |
| [Blazor Server](https://) | Frontend / UI        |
| [MudBlazor](https://)     | UI Objects and Theme |