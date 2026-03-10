YCS has implemented an alarm mechanism. When specific conditions occur on the server, alarm events are reported in the alarm log. When the conditions are resolved, the alarm events are cleared. There are also some alarm events that only need to be reported and do not require clearing, such as the abnormal offline of a database instance. This is generally caused by business logic rather than internal YCS errors. After manual intervention and repair, it can be automatically cleared, and the system reports the alarm without needing to track this event further.

## Alarm Events

### AlertReset

An alarm is reported after a successful cluster startup. If this tag is encountered, it indicates that any previously triggered alarms without a clear tag do not need to be cleared.

This alarm event does not require clearing.

Handling Suggestions:

- No action required.

### ClusterSeparated

When a fault occurs in the cluster and a server is kicked out of the cluster, an alarm is reported. The server will attempt to rejoin the cluster, and when the fault is resolved and the server successfully rejoins, the alarm is cleared.

This alarm event requires clearing.

Handling Suggestions:

- Check if the network connection between servers is normal.
- Check if the server can normally access shared storage.

### DbInstanceStopped

When the monitoring thread detects that a database instance has gone offline abnormally, an alarm is reported. The instance is restarted and cleared when it reconnects successfully with YCS.

This alarm event requires clearing.

Handling Suggestions:

- Check if the database data file has been deleted or corrupted.
- Check if there is sufficient disk space.

### DiskError

When a fault occurs in the cluster and there is an abnormal access to the voting disk, an alarm is reported. The server will attempt to rejoin the cluster, and when the fault is resolved and the server successfully rejoins, the alarm is cleared.

This alarm event requires clearing.

Handling Suggestions:

- Check if the network connection between servers is normal.
- Check if the server can normally access shared storage.

### InterChannelClosed

An abnormal closure of the network connection between servers is reported as an alarm. The alarm is cleared after the network connection is re-established between the servers.

This alarm event requires clearing.

Handling Suggestions:

- Check if the network connection between servers is normal.
- Check if the listening port on the service-side server is normal.

### NewVoteExpected

When a "brain split" occurs in the cluster and a reelection is underway, the server perceives the anomaly and reports an alarm. The server will attempt to rejoin the cluster, and the alarm is cleared when the server successfully rejoins.

This alarm event requires clearing.

Handling Suggestions:

- Check if the network connection between servers is normal.
- Check if the server can normally access shared storage.

### YcsInstanceExited

If the YCS process on the server exits abnormally due to an unmanageable internal error, it reports an alarm. YCSM will restart YCS in an attempt to restore service, and the alarm is cleared after the server starts successfully.

This alarm event requires clearing.

Handling Suggestions:

- Check if the network connection between servers is normal.
- Check if the server can access the shared storage normally.
- Check if the configurations for YFS are normal.

### RootAgentFailure

When YCS requests YCSRA to perform a specific operation and fails, an alarm is reported. YCS will continuously restart until the request to YCSRA is successfully executed, clearing the alarm.

This alarm event requires clearing.

Handling Suggestions:

- Check if the YCSRA process is started on the server and if its status is normal.
- If the process is normal, investigate the error reason based on the specific error messages in the YCSRA running logs.

## Alarm Log

The alarm log is located in the log/alert folder under the YCS HOME directory, named alert.log. The path and name cannot be modified.

The alarm log does not have a switch control; it is always in an open state, continuously maintaining the log file. There is no set limit on the file size, and when the alert.log file becomes too large, manual cleanup of the alarm log is required.

```verilog
$ cat $YASCS_HOME/log/alert/alert.log
2023-07-19 09:49:47.936|0|AlertReset|0|0||reset all reported alert
2023-07-19 09:52:16.096|0|AlertReset|0|0||reset all reported alert
2023-07-19 10:08:20.179|0|DbInstanceStopped|1|0||yasdb had stopped, that resource id is: 1
2023-07-19 10:08:21.363|0|DbInstanceStopped|1|1|
2023-07-19 10:09:08.206|0|DbInstanceStopped|1|0||yasdb had stopped, that resource id is: 1
2023-07-19 10:09:09.358|0|DbInstanceStopped|1|1|
```

### Common Alarm Examples

#### Server Link Abnormal Disconnection

inter channel closed: When the link between nodes is abnormally closed, an alarm log is recorded, and when the link is restored, a log of the cleared alarm is recorded.

***Example***

```verilog
//On 2023-06-13 11:20:15.391, node 1 is abnormally disconnected from this node, triggering the InterChannelClosed alarm event.
//The first 1 indicates the node ID, followed by 0, indicating that the alarm has been generated.
//On 2023-06-13 11:20:50.366, node 1 reconnects to this node, clearing this alarm.
//The last 1 indicates the clearing of the alarm.
2023-06-13 11:20:15.391|0|InterChannelClosed|1|0||node id: 1 had disconnected
2023-06-13 11:20:50.366|0|InterChannelClosed|1|1|
```

#### Abnormal Access to Voting Disk

disk error: An alarm is triggered when there is an abnormal access to the voting disk. When the system's access to the voting disk recovers, the alarm is cleared.

Abnormal access to the voting disk includes: read disk abnormality, write disk abnormality.

***Example***

```verilog
2023-06-13 11:39:01.858|0|DiskError|0|0||voting file can't access
2023-06-13 11:39:01.858|0|DiskError|0|1|
```

#### Database Abnormal Stop

db instance stopped: An alarm log is recorded when YashanDB resource stops. A log of the cleared alarm is recorded after it successfully reconnects.

***Example***

```verilog
//On 2023-06-13 11:48:37.593, the database with resource ID 1 is found to have stopped, triggering the DbInstanceStopped alarm event.
//The number 1 after DbInstanceStopped indicates the resource ID.
//On 2023-06-13 11:48:38.747, after the resource with ID 1 is found to have reconnected to Ycs, this alarm is cleared.
2023-06-13 11:48:37.593|0|DbInstanceStopped|1|0||yasdb had stopped, that resource id is: 1
2023-06-13 11:48:38.747|0|DbInstanceStopped|1|1|
```
