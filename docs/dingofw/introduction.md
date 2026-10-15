[![Donate](https://img.shields.io/badge/Donate-PayPal-blue.svg)](https://www.paypal.com/donate/?hosted_button_id=HDE8YCVY9NR2L)
[![Discord](https://img.shields.io/discord/1243358184266010667?label=discord)](https://discord.com/invite/TxnPYQkVu3)
[![GitHub Release](https://img.shields.io/github/v/release/corygrant/dingoFW?include_prereleases&display_name=tag)](https://github.com/corygrant/dingoFW/releases)
[![GitHub last commit](https://img.shields.io/github/last-commit/corygrant/dingoFW)](https://github.com/corygrant/dingoFW/commits/master/)
[![GitHub Issues or Pull Requests](https://img.shields.io/github/issues/corygrant/dingoFW)](https://github.com/corygrant/dingoFW/issues)

dingoFW is the firmware for all dingoElectronics devices. It uses the [ChibiOS](https://www.chibios.org/dokuwiki/doku.php) RTOS.

![Chibi](../images/chibi.png)

All devices share the same code. Each device has a board folder (`boards/<board>`) with its pins, outputs/inputs and cyclic messages, and the board is selected when building.

| Board          | Device        | MCU            |
| -------------- | ------------- | -------------- |
| `dingopdm_v7`    | dingoPDM (v7.x) | STM32F446RET6 |
| `dingopdmmax_v1` | dingoPDM-Max (v1.x) | STM32F446RET6 |
| `canboard_v2`    | CANBoard (v2) | STM32F303K8 |

## Download

The latest version can be downloaded here: [dingoFW Releases](https://github.com/corygrant/dingoFW/releases)

Each release has a `.hex`, `.bin` and `.elf` file for each board, for example `dingopdm_v7_FW_v0-6-1.hex`. Use the file that matches your board.

!!! Note
    `testing-latest` is a pre-release built from the `testing` branch. It has the newest changes but has had less testing.

## Update

See [Updating](./updating.md) for firmware updating instructions

## Build

To build dingoFW on your machine refer to [Build](./build.md)

## Custom PCB Firmware

dingoFW was designed to be easily modified to work on custom PCBs with varying numbers of outputs and inputs. 

See [Customization](./custom.md) for information on adding a custom board to dingoFW. 

## Contributing

See [CONTRIBUTING.md](https://github.com/corygrant/dingoElec/blob/main/CONTRIBUTING.md) for the branch and pull request flow.
