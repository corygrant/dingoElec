# Adapters

dingoConfig talks to devices through one adapter at a time. 

Press the `Adapter` button in the top right to pick the adapter, port and bitrate, then press connect. The adapter, port and bitrate are remembered for next time.

| Adapter     | Port            | Bitrate  | Systems |
| ----------- | --------------- | -------- | ------- |
| `USB`       | Serial port     | Not used | All |
| `SLCAN`     | Serial port     | Selected | All |
| `PCAN`      | `PCAN_USBBUS1`  | Selected | All |
| `SocketCAN` | `can0`, `can1`, ... | Set in Linux | Linux |
| `Sim`       | None            | Not used | All |

The bitrate must match the CAN bus (`1000K`, `500K`, `250K`, `125K` or `100K`). dingoPDM defaults to `500K`.

!!! Note
    The adapter shows `Connected` only while messages are being received. If no device is sending, the adapter will show `Not Connected`.

## USB

Connect directly to a dingoPDM or dingoPDM-Max with a USB-C cable. The device shows up as a serial port (COM port on Windows, `/dev/ttyACM*` on Linux).

When `Connect USB to CAN` is enabled on the device (default), the dingoPDM also works as a USB to CAN adapter:

- CAN messages the dingoPDM receives are forwarded to dingoConfig
- Messages from dingoConfig are sent out on the CAN bus
- Every device on the CAN bus (other dingoPDMs, CANBoards, keypads) can be configured and monitored through the one USB connection, no separate adapter is needed
- Messages to and from the connected dingoPDM's own config IDs (`Base ID + 0`, `Base ID + 1`) are not forwarded

When it is disabled, only the device connected by USB can be configured.

!!! Note
    The CAN bus bitrate is the dingoPDM's bitrate, the bitrate setting in dingoConfig isn't used for USB.

!!! Note
    V7.2 dingoPDM PCBs do not support USB-C to USB-C cables, use a USB-C to USB-A cable.

## SLCAN

Any USB to CAN adapter that uses the SLCAN (Lawicel) serial protocol, for example CANable (slcan firmware) or [USB2CAN](https://github.com/corygrant/USB2CAN_HW).

- Every device on the CAN bus can be configured at the same time
- Select the serial port and the bitrate of the CAN bus

## PCAN

[PEAK-System PCAN-USB](https://www.peak-system.com/PCAN-USB.199.0.html?&L=1) adapters. Install the PEAK driver (Windows) or the PCAN-Basic library first. dingoConfig uses the first PCAN-USB channel (`PCAN_USBBUS1`).

## SocketCAN

Linux only. Any adapter supported by SocketCAN (for example CANable with candleLight firmware, PCAN on Linux).

The bitrate is set in Linux, not in dingoConfig. Bring the interface up before connecting:

```bash
sudo ip link set can0 type can bitrate 500000
sudo ip link set up can0
```

## Sim

Simulated adapter for testing without hardware. With `Sim` selected, `Open Log File` loads a [CAN log](./canlog.md) `.csv` file and plays it back as if it were live CAN traffic. Play, pause, reset and loop controls are shown in the top bar.
