This view displays the session information of running parallel tasks.

|Field |Type |Description |
| ---                     | ---         | --- |
| GROUP_ID               | NUMBER      | Group ID     |
| GROUP_NODE_ID          | NUMBER      | Node ID within the group |
| INST_ID                | NUMBER      | Instance ID  |
| SID                    | SMALLINT    | Session ID of the parallel task |
| SERIAL#                | INTEGER     | Sequence number of the session |
| PADDR                  | BIGINT      | Address of the thread executing this session, i.e., the address of the parallel worker's thread |
| QCSID                  | SMALLINT    | Session ID of the parallel coordinator |
| QCSERIAL#              | INTEGER     | Sequence number of the parallel coordinator's session |
| PARENT_STAGE_ID        | SMALLINT    | Parent stage ID |
| STAGE_ID               | SMALLINT    | Stage ID, a stage is the smallest task executed by a parallel worker, usually consisting of one sender and several receivers or table scans |
| SLICE_ID               | SMALLINT    | Slice ID in the current stage; each stage can be divided into multiple logical slices, and the maximum slice ID during execution is the number of workers allocated minus one |
| TQ_ID                  | SMALLINT    | Sending table queue ID; the entry of each stage is the table queue ID where the parallel sending end is located |
| DEGREE                 | SMALLINT    | Parallelism during execution, i.e., the number of workers allocated |
| REQ_DEGREE             | SMALLINT    | Desired execution parallelism |