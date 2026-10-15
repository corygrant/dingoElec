# CAN Log

The `CAN Log` page shows every CAN message sent or received by the adapter.

| Item            | Description |
| --------------- | ----------- |
| ID Format       | `Hex` = ID in hexadecimal (`0x7D0`), `Dec` = ID in decimal (`2000`) |
| Data Format     | `Hex` = data bytes in hexadecimal (`0xB3`), `Dec` = data bytes in decimal (`179`) |
| Message Summary | One row per CAN ID with the latest data |
| Full History    | Every message sent or received, ordered by time. Click a column header to sort |
| Show last       | Number of messages shown in Full History |
| Dir             | `RX` = received by the adapter, `TX` = sent by the adapter |
| Log to File     | Save every CAN message to a CSV file |
| Clear           | Clear all messages from the log |

## Log Files

Log files are saved in the `logs` folder next to the dingoConfig program, named `canlog_YYYYMMDD_HHMMSS.csv`.

```
Timestamp,Direction,CAN ID,Length,Data
2024-12-30 10:46:29.123,Rx,0x123,8,12 34 56 78 AB CD EF 00
```

Log files can be played back with the [Sim adapter](./adapters.md#sim).

!!! Tip
    When asking for help or reporting a bug, a CAN log file helps a lot.
