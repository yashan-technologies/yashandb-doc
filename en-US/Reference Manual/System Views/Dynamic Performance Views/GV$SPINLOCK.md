This view displays information about spin locks.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID     | NUMBER   | Group ID                                                     |
| GROUP_NODE_ID| NUMBER   | Node ID within the group                                     |
| INST_ID      | NUMBER   | Instance ID                                                  |
| NAME         | VARCHAR(32) | Name of the spin lock                                       |
| SPINCOUNT    | INTEGER  | Number of attempts to acquire the lock before each sleep    |
| TIMES        | BIGINT   | Number of times waiting for the lock; this field can indicate the level of spin lock contention |