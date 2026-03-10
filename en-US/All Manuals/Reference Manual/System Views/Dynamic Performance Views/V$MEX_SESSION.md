This view displays the usage of the MEX pool in the various sessions of the current node.

|Field |Type |Description |
|---------------------|----------|-------------------|
| SID                 | SMALLINT  | Session ID                        |
| MAX_HOLD_SIZE       | BIGINT    | Maximum memory allowed to hold (in bytes) |
| HOLD_SIZE           | BIGINT    | Memory held (in bytes)           |
| USING_SIZE          | BIGINT    | Memory in use (in bytes)         |
| UNUSED_SIZE         | BIGINT    | Memory unused (in bytes)         |
| TOTAL_ALLOC_TIMES   | BIGINT    | Historical allocation times       |
| TOTAL_ALLOC_SIZE    | BIGINT    | Historical allocation size (in bytes) |
| TOTAL_FREE_TIMES    | BIGINT    | Historical release times          |
| TOTAL_FREE_SIZE     | BIGINT    | Historical release size (in bytes) |
| TOTAL_FILL_TIMES    | BIGINT    | Historical buffer fill times (buffer miss times) |
| TOTAL_GC_TIMES      | BIGINT    | Historical garbage collection times |