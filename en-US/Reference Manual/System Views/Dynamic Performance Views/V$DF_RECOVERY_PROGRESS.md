This view displays the summary information of the apply progress for tablespace files.

It is applicable only to Standalone Deployment; in other deployment types, this view is meaningless.

|Field |Type |Description |
| --- | --- | --- |
| RCY_START       | INTEGER | ASN of the log where apply starts    |
| RCY_START_LFN   | BIGINT  | LFN of the log where apply starts     |
| RCY_END         | INTEGER | ASN of the log where apply ends      |
| RCY_END_LFN     | BIGINT  | LFN of the log where apply ends       |
| REPLAY_POINT    | INTEGER | ASN of the current apply log         |
| REPLAY_POINT_LFN | BIGINT  | LFN of the current apply log          |