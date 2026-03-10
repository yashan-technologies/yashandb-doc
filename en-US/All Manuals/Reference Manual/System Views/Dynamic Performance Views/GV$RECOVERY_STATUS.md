This view displays information about the log apply status.

|Field |Type |Description |
| ----- | ----------- | ------------ |
| GROUP_ID       | NUMBER      | Group ID                                   |
| GROUP_NODE_ID  | NUMBER      | Node ID within the group                   |
| INST_ID        | NUMBER      | Instance ID                                |
| THREAD#        | TINYINT     | Instance number of the database            |
| STATUS         | VARCHAR(16) | Database's apply status<br>\* NONE: No log apply required currently<br>\* RUNNING: Currently applying logs<br>\* PAUSED: Log apply has been paused<br>\* ERROR: A fault occurred during the log apply in the current database|
| PARALLELISM    | SMALLINT    | Number of parallel apply threads; value is 0 when parallel apply is not enabled, or a power of 2 when enabled |
| START_RECOVERY_TIME  | DATE  | SCN to start applying                     |
| STOP_RECOVERY_TIME   | DATE  | SCN to stop applying                      |
| REPLAY_POINT   | VARCHAR(32) | Current log apply point, {resetid}-{asn}-{blockid}-{lfn} |



> **Note**: 
>
> {resetid}-{asn}-{blockid}-{lfn}
>
> - resetid: The reset id of the redo log, which increments by 1 each time the redo timeline is reset.
> - asn: Archive sequence number, which increments by 1 for every generated redo, each redo has a different asn.
> - blockid: The ID of the page within the redo file, where the offset of the page is block id * block size.
> - lfn: Log flush number, which increments by 1 for each redo flush to disk.

