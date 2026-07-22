This view displays information about the log apply status.

|Field |Type |Description |
| ----- | ----------- | ------------ |
| THREAD#             | TINYINT     | Instance number of the database                       |
| STATUS              | VARCHAR(16) | Database apply status<br>\* NONE: No log apply needed currently<br>\* RUNNING: Currently applying logs<br>\* PAUSED: Log apply paused<br>\* IDLE: The current database replay thread is in idle state |
| PARALLELISM         | SMALLINT    | Number of parallel apply threads; 0 when parallel apply is not enabled, can only be a power of 2 when enabled |
| START_RECOVERY_TIME | DATE        | SCN when apply started                               |
| STOP_RECOVERY_TIME  | DATE        | SCN when apply stopped                               |
| REPLAY_POINT        | VARCHAR(32) | Current log apply point, {resetid}-{asn}-{blockid}-{lfn} |



> **Note**: 
>
> {resetid}-{asn}-{blockid}-{lfn}
>
> - resetid: The reset id of the redo log, which increments by 1 each time the redo timeline is reset.
> - asn: Archive sequence number, which increments by 1 for every generated redo, each redo has a different asn.
> - blockid: The ID of the page within the redo file, where the offset of the page is block id * block size.
> - lfn: Log flush number, which increments by 1 for each redo flush to disk.

