# Configuration Storage

The device configuration is a list of [parameters](./can.md#parameters). The saved copy is stored with a version number and a CRC32 checksum so the firmware can tell if it is valid.

| Device                          | Storage |
| ------------------------------- | ------- |
| dingoPDM, dingoPDM-Max          | External FRAM over I2C |
| CANBoard                        | Internal MCU flash |

## Read

On power up, after starting ChibiOS and I2C, the firmware reads the configuration:

1. Read the configuration starting at address 0
2. Check that the stored version matches the version the firmware expects
3. Read the CRC32 stored right after the configuration
4. Calculate the CRC32 of the configuration that was read and check that it matches
5. Continue starting up

## Read Failure

If any read step fails (no configuration saved, configuration from a different firmware version, or a bad CRC):

1. All parameters are set to their default values
2. The default configuration is written to memory
    - If the write fails, the device goes to the [error state](./errors.md) with error `4` (Configuration)
3. The device continues starting up with the default configuration

!!! Info
    Some firmware updates change the configuration format. After one of those updates, the saved configuration no longer matches and the device starts with the default (blank) configuration.

    Write your configuration back to the device with dingoConfig (`Write All`, then `Burn`). Keep a [config file](../dingoconfig/configfiles.md) saved so this is quick.

## Updating

Parameters are changed with CAN or USB messages, see [Parameters](./can.md#parameters). No special mode is needed, parameters can be changed while the device is running.

!!! Note
    A changed value is used immediately, but is not saved over a power cycle.

    Send a [Burn](./can.md#burn) to save the configuration to memory.

A new Base ID is used right away. A new CAN bitrate is only used after a [Restart](./can.md#restart) (or power cycle), Burn before restarting or the change is lost.

## Write

To reduce memory writes, the configuration is only written when a [Burn](./can.md#burn) message is received.

Write sequence:

1. Check the FRAM ID (dingoPDM)
2. Write the configuration starting at address 0
3. Calculate the CRC32 of the configuration
4. Write the CRC32 right after the configuration
5. Send the result in the Burn response (`1` = success, `0` = fail)
