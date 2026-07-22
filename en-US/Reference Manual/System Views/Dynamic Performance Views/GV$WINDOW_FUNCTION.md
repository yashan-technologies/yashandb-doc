This view displays all window function information provided by the current system.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID     | NUMBER     | Group ID                                                                                    |
| GROUP_NODE_ID| NUMBER     | Node ID within the group                                                                    |
| INST_ID      | NUMBER     | Instance ID                                                                                 |
| ID           | SMALLINT   | Enum of Function ID<br>0 AVG  <br>1 COUNT  <br>2 DENSE_RANK  <br>3 FIRST  <br>4 FIRST_VALUE  <br>5 LAG  <br>6 LAST  <br>7 LAST_VALUE  <br>...... |
| NAME         | VARCHAR(64)| Function name                                                                                |
| ISAGGR       | VARCHAR(8) | Whether it is an AGGR function<br>0 Y  <br>1 Y  <br>2 N  <br>3 N  <br>4 N  <br>5 N  <br>6 N <br>7 N  <br>......                                  |