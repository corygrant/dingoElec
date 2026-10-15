# Config Files

A config file saves every device you've added and all of their settings. Use it to back up a configuration, copy it to another device, or restore it after a firmware update.

The file buttons are in the top bar:

| Button     | Description |
| ---------- | ----------- |
| `New`      | Remove all devices and start a new file |
| `Open`     | Open a config file |
| `Save`     | Save to the open file |
| `Save As`  | Save to a new file |
| `Settings` | Working directory and ID display format (hex or decimal) |

The open file name is shown in the top bar.

## Location

Config files are `.json` files saved in the working directory, by default:

| System  | Folder |
| ------- | ------ |
| Windows | `Documents\dingoConfig` |
| Linux   | `~/Documents/dingoConfig` |
| macOS   | `~/Documents/dingoConfig` |

The working directory can be changed in `Settings`.

## Restoring a Configuration

1. `Open` the config file
2. Connect to the device
3. Press `Write All` on the device page
4. Press `Burn`

!!! Tip
    Save a config file before every firmware update. Some updates reset the device to the default configuration, see [Configuration Storage](../dingofw/configuration.md#read-failure).
