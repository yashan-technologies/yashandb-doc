The YCS alert log is located in the log/alert folder under the YCS HOME directory, named alert.log. The path and name cannot be modified.

The alert log does not have a switch control; it is always in an open state, continuously maintaining the log file. There is no set limit on the file size, and when the alert.log file becomes too large, manual cleanup of the alert log is required.

```verilog
$ cat $YASCS_HOME/log/alert/alert.log
2023-07-19 09:49:47.936|0|AlertReset|0|0||reset all reported alert
2023-07-19 09:52:16.096|0|AlertReset|0|0||reset all reported alert
2023-07-19 10:08:20.179|0|DbInstanceStopped|1|0||yasdb had stopped, that resource id is: 1
2023-07-19 10:08:21.363|0|DbInstanceStopped|1|1|
2023-07-19 10:09:08.206|0|DbInstanceStopped|1|0||yasdb had stopped, that resource id is: 1
2023-07-19 10:09:09.358|0|DbInstanceStopped|1|1|
```

## Server Link Abnormal Disconnection

inter channel closed: When the link between nodes is abnormally closed, an alert log is recorded, and when the link is restored, a log of the cleared alarm is recorded.

***Example***

```verilog
//On 2023-06-13 11:20:15.391, node 1 is abnormally disconnected from this node, triggering the InterChannelClosed alarm event.
//The first 1 indicates the node ID, followed by 0, indicating that the alarm has been generated.
//On 2023-06-13 11:20:50.366, node 1 reconnects to this node, clearing this alarm.
//The last 1 indicates the clearing of the alarm.
2023-06-13 11:20:15.391|0|InterChannelClosed|1|0||node id: 1 had disconnected
2023-06-13 11:20:50.366|0|InterChannelClosed|1|1|
```

## Abnormal Access to Voting Disk

disk error: An alarm is triggered when there is an abnormal access to the voting disk. When the system's access to the voting disk recovers, the alarm is cleared.

Abnormal access to the voting disk includes: read disk abnormality, write disk abnormality.

***Example***

```verilog
2023-06-13 11:39:01.858|0|DiskError|0|0||voting file can't access
2023-06-13 11:39:01.858|0|DiskError|0|1|
```

## Database Abnormal Stop

db instance stopped: An alert log is recorded when YashanDB resource stops. A log of the cleared alarm is recorded after it successfully reconnects.

***Example***

```verilog
//On 2023-06-13 11:48:37.593, the database with resource ID 1 is found to have stopped, triggering the DbInstanceStopped alarm event.
//The number 1 after DbInstanceStopped indicates the resource ID.
//On 2023-06-13 11:48:38.747, after the resource with ID 1 is found to have reconnected to Ycs, this alarm is cleared.
2023-06-13 11:48:37.593|0|DbInstanceStopped|1|0||yasdb had stopped, that resource id is: 1
2023-06-13 11:48:38.747|0|DbInstanceStopped|1|1|
```
