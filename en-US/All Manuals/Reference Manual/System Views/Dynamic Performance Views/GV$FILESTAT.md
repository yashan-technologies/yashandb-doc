This view shows the number of completed physical read and write operations, along with the total number of single and multi-block I/O operations completed at the file level.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID        | NUMBER  | Group ID                                   |
| GROUP_NODE_ID   | NUMBER  | Node ID within the group                   |
| INST_ID         | NUMBER  | Instance ID                                |
| FILE#           | INTEGER | Data file number                           |
| PHYRDS          | BIGINT  | Completed physical read counts              |
| PHYWRTS         | BIGINT  | Number of writes that DBWR needs to perform |
| PHYBLKRD        | BIGINT  | Number of physical blocks read              |
| PHYBLKWRT       | BIGINT  | Number of blocks written in PHYWRTS        |
| SINGLEBLKRDS    | BIGINT  | Number of single block reads                |
| READTIM         | BIGINT  | Time taken to execute reads (in hundredths of a second)  |
| WRITETIM        | BIGINT  | Time taken to execute writes (in hundredths of a second) |
| SINGLEBLKRDTIM  | BIGINT  | Cumulative time for single block reads (in hundredths of a second) |
| AVGIOTIM        | BIGINT  | Average time spent on I/O (in hundredths of a second) |
| LSTIOTIM        | BIGINT  | Time taken to execute the last I/O (in hundredths of a second) |
| MINIOTIM        | BIGINT  | Shortest time taken for a single I/O (in hundredths of a second) |
| MAXIORTM        | BIGINT  | Longest time taken for a single read (in hundredths of a second) |
| MAXIOWTM        | BIGINT  | Longest time taken for a single write (in hundredths of a second) |