This view displays information about spin locks.

|Field |Type |Description |
| --- | --- | --- |
| NAME     | VARCHAR(32) | The name of the spin lock                        |
| SPINCOUNT| INTEGER     | The number of attempts to acquire the lock before each sleep |
| TIMES    | BIGINT      | The number of times the lock was waited on; this field can be used to assess the degree of contention for the spin lock |