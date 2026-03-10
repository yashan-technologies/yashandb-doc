This view shows the number of completed physical read and write operations, as well as the total number of single and multi-block I/O completed at the file level.

|Field |Type |Description |
| --- | --- | --- |
| FILE#            | INTEGER  | Data file number                          |
| PHYRDS           | BIGINT   | Completed physical read operations        |
| PHYWRTS          | BIGINT   | Number of writes needed by DBWR          |
| PHYBLKRD         | BIGINT   | Number of physical blocks read            |
| PHYBLKWRT        | BIGINT   | Number of blocks written for PHYWRTS      |
| SINGLEBLKRDS     | BIGINT   | Number of single block reads               |
| READTIM          | BIGINT   | Time taken to perform read (in centiseconds) |
| WRITETIM         | BIGINT   | Time taken to perform write (in centiseconds) |
| SINGLEBLKRDTIM   | BIGINT   | Cumulative single block read time (in centiseconds) |
| AVGIOTIM         | BIGINT   | Average time spent on I/O (in centiseconds) |
| LSTIOTIM         | BIGINT   | Time taken to perform the last I/O (in centiseconds) |
| MINIOTIM         | BIGINT   | Shortest time taken for a single I/O (in centiseconds) |
| MAXIORTM         | BIGINT   | Longest time taken to perform a single read (in centiseconds) |
| MAXIOWTM         | BIGINT   | Longest time taken to perform a single write (in centiseconds) |