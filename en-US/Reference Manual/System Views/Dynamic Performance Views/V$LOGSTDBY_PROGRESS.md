This view shows the apply progress of the logical standby database.

|Field |Type |Description |
|-------|------|------|
| APPLIED\_SCN   | BIGINT    | All transactions with COMMIT SCN less than or equal to this SCN have been applied |
| APPLIED\_TIME  | TIMESTAMP(6) | SCN corresponding to APPLIED\_SCN                               |
| APPLIED\_LSN   | BIGINT    | Maximum LSN of the transactions that have been applied          |
| APPLIED\_POINT | VARCHAR(64)| Current log apply point, {resetid}-{asn}-{blockid}-{lfn}          |
| RESTART\_SCN   | BIGINT    | After service restart, YStream will not read log files with SCN less than this SCN |
| RESTART\_TIME  | TIMESTAMP(6) | SCN corresponding to RESTART\_SCN                               |
| RESTART\_LSN   | BIGINT    | Starting LSN of the log parsing after service restart           |
| CAPTURE\_SCN   | BIGINT    | Current parsed log SCN                                          |
| CAPTURE\_TIME  | TIMESTAMP(6) | SCN corresponding to CAPTURE\_SCN                               |
| CAPTURE\_LSN   | BIGINT    | LSN of the logs parsed by YStream                               |
| RECEIVE\_SCN   | BIGINT    | Current log receive SCN of the Standby database                 |
| RECEIVE\_TIME  | TIMESTAMP(6) | SCN corresponding to RECEIVE\_SCN                               |
| RECEIVE\_LSN   | BIGINT    | Current log receive LSN of the Standby database                 |
| RECEIVE\_POINT | VARCHAR(64)| Current log receive point of the Standby database, {resetid}-{asn}-{blockid}-{lfn} | 



> **Note**: 
>
> {resetid}-{asn}-{blockid}-{lfn}
>
> - resetid: The reset id of the redo log, which increments by 1 each time the redo timeline is reset.
> - asn: Archive sequence number, which increments by 1 for every generated redo, each redo has a different asn.
> - blockid: The ID of the page within the redo file, where the offset of the page is block id * block size.
> - lfn: Log flush number, which increments by 1 for each redo flush to disk.

