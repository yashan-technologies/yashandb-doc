This view shows the apply progress of the logical standby database.

|Field |Type |Description |
|-------|------|------|
| APPLIED_SCN   | BIGINT    | All transactions with COMMIT SCN less than or equal to this SCN have been applied |
| APPLIED_TIME  | TIMESTAMP | SCN corresponding to APPLIED_SCN                               |
| APPLIED_LSN   | BIGINT    | Maximum LSN of the transactions that have been applied          |
| APPLIED_POINT | VARCHAR(64)| Current log apply point, {resetid}-{asn}-{blockid}-{lfn}          |
| RESTART_SCN   | BIGINT    | After service restart, YStream will not read log files with SCN less than this SCN |
| RESTART_TIME  | TIMESTAMP | SCN corresponding to RESTART_SCN                               |
| RESTART_LSN   | BIGINT    | Starting LSN of the log parsing after service restart           |
| CAPTURE_SCN   | BIGINT    | Current parsed log SCN                                          |
| CAPTURE_TIME  | TIMESTAMP | SCN corresponding to CAPTURE_SCN                               |
| CAPTURE_LSN   | BIGINT    | LSN of the logs parsed by YStream                               |
| RECEIVE_SCN   | BIGINT    | Current log receive SCN of the Standby database                 |
| RECEIVE_TIME  | TIMESTAMP | SCN corresponding to RECEIVE_SCN                               |
| RECEIVE_LSN   | BIGINT    | Current log receive LSN of the Standby database                 |
| RECEIVE_POINT | VARCHAR(64)| Current log receive point of the Standby database, {resetid}-{asn}-{blockid}-{lfn} | 



> **Note**: 
>
> {resetid}-{asn}-{blockid}-{lfn}
>
> - resetid: The reset id of the redo log, which increments by 1 each time the redo timeline is reset.
> - asn: Archive sequence number, which increments by 1 for every generated redo, each redo has a different asn.
> - blockid: The ID of the page within the redo file, where the offset of the page is block id * block size.
> - lfn: Log flush number, which increments by 1 for each redo flush to disk.

