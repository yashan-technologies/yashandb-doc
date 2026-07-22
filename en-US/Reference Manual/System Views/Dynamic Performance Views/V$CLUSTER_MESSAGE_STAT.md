This view displays the statistics of YAC message interaction.

|Field |Type |Description |
| --- | --- | --- |
| ID | INTEGER | Message sequence number |
| NAME | VARCHAR(32) | Message name |
| MESSAGE\_GROUP | VARCHAR(32) | Message group<br>\* AXC\_FG\_TASK: Front-end thread<br>\* AXC\_GRC\_TASK: GRC thread<br>\* AXC\_GCS\_TASK: GCS thread<br>\* AXC\_GLS\_TASK: GLS thread<br>\* AXC\_REFORM\_TASK: Reform thread<br>\* AXC\_BATCH\_TASK: Batch processing thread<br>\* AXC\_BCST\_TASK: DDL type thread<br>\* AXC\_AUX\_TASK: Auxiliary thread<br>\* AXC\_SYNC\_TASK: Synchronization thread<br>\* AXC\_SMON\_TASK: Deadlock detection thread<br>\* AXC\_RCYTEMP\_TASK: Temporary table space recovery thread |
| SEND\_TIMES | BIGINT | Number of successful message sends |
| SEND\_FAILED\_TIMES | BIGINT | Number of failed message sends |
| SEND\_TOTAL\_COSTS | BIGINT | Total time spent on successful message sends (unit: microseconds) |
| SEND\_AVG\_COST | INTEGER | Average time spent on successful message sends (unit: microseconds) |
| SEND\_MAX\_COST | INTEGER | Maximum time spent on successful message sends (unit: microseconds) |
| RECV\_TIMES | BIGINT | Number of message receipts |
| RECV\_TOTAL\_COSTS | BIGINT | Total time spent on message receipts (unit: microseconds) |
| RECV\_AVG\_COST | INTEGER | Average time spent on message receipts (unit: microseconds) |
| RECV\_MAX\_COST | INTEGER | Maximum time spent on message receipts (unit: microseconds) |
| WAIT\_TIMES | BIGINT | Number of times asynchronous messages waited for processing |
| WAIT\_TOTAL\_COSTS | BIGINT | Total time spent queuing asynchronous messages (unit: microseconds) |
| WAIT\_AVG\_COST | INTEGER | Average time spent queuing asynchronous messages (unit: microseconds) |
| WAIT\_MAX\_COST | INTEGER | Maximum time spent queuing asynchronous messages (unit: microseconds) |
| PROCESS\_TIMES | BIGINT | Number of successful message processes |
| PROCESS\_FAILED\_TIMES | BIGINT | Number of failed message processes |
| PROCESS\_TOTAL\_COSTS | BIGINT | Total time spent on successful message processes (unit: microseconds) |
| PROCESS\_AVG\_COST | INTEGER | Average time spent on successful message processes (unit: microseconds) |
| PROCESS\_MAX\_COST | INTEGER | Maximum time spent on successful message processes (unit: microseconds) |