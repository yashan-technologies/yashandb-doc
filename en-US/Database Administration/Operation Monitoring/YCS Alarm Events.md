YCS has implemented an alarm mechanism. When specific conditions occur on the server, alarm events are reported in the alarm log. When the conditions are resolved, the alarm events are cleared. There are also some alarm events that only need to be reported and do not require clearing, such as the abnormal offline of a database instance. This is generally caused by business logic rather than internal YCS errors. After manual intervention and repair, it can be automatically cleared, and the system reports the alarm without needing to track this event further.

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

