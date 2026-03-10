This view displays information related to the full database flashback functionality.

It is only applicable to Standalone Deployment; this view has no meaning in other deployment forms.

|Field |Type |Description |
| --- | --- | --- |
| OLDEST_FLASHBACK_SCN    | BIGINT  | The SCN corresponding to the oldest point in time that the full database flashback can reach |
| OLDEST_FLASHBACK_TIME    | TIMESTAMP | The timestamp corresponding to the oldest point in time that the full database flashback can reach |
| RETENTION_TARGET          | BIGINT  | The maximum retention duration of the full database flashback log files (in minutes). Log files that exceed this duration are eligible for automatic cleanup |
| TOTAL_FILE_SIZE         | BIGINT  | The total space occupied by all log files of the current full database flashback (in bytes) |