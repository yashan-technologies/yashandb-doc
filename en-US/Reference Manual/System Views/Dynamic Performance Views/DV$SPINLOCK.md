This view shows the information of spin locks for all nodes in the ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP\_ID | INTEGER | Group ID |
| GROUP\_NODE\_ID | INTEGER | Node ID within the group |
| NAME | VARCHAR(32) | Name of the spin lock |
| SPINCOUNT | INTEGER | Number of attempts to acquire the lock before each sleep |
| TIMES | BIGINT | Number of lock acquisition waits; this field can be used to determine the level of spin lock contention |