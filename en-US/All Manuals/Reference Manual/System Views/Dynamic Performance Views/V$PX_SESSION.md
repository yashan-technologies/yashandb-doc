This view shows the session information for running parallel tasks.

|Field |Type |Description |
| ---                     | ---         | --- |
| SID                     | SMALLINT     | Session ID of the parallel task |
| SERIAL#                 | INTEGER      | Sequence number of the session |
| PADDR                   | BIGINT       | Address of the thread executing the session, that is, the thread address of the parallel worker |
| QCSID                   | SMALLINT     | Session ID of the parallel coordinator |
| QCSERIAL#               | INTEGER      | Sequence number of the parallel coordinator's session |
| PARENT_STAGE_ID         | SMALLINT     | Parent stage ID |
| STAGE_ID                | SMALLINT     | Stage ID; a stage is the smallest task executed by the parallel worker, usually consisting of one sender and several receivers or table scans |
| SLICE_ID                | SMALLINT     | Slice ID in the current stage; each stage can be divided into multiple logical slices, with the maximum slice ID during execution being the number of allocated workers minus one |
| TQ_ID                   | SMALLINT     | Table queue ID of the sending table; the entry of each stage is the table queue ID where the parallel sending end is located |
| DEGREE                  | SMALLINT     | Degree of parallelism during execution, that is, the number of allocated workers |
| REQ_DEGREE              | SMALLINT     | Desired execution degree of parallelism |