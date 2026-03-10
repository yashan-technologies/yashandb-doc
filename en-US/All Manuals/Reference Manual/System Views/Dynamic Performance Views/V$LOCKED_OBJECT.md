This view displays the lock information for all current objects.

|Field |Type |Description |
| ---------- |------------| ------------------------------------------------------------ |
| XEXT       | SMALLINT   | The transaction area number corresponding to the current transaction  |
| XNODE      | SMALLINT   | The number of the current transaction within the corresponding transaction area |
| XSN        | INTEGER    | The version number of the current transaction              |
| OBJECT_ID  | BIGINT     | The ID of the table                                       |
| SESSION_ID | SMALLINT   | The session ID                                            |
| LMODE      | VARCHAR(8) | The type of lock held by the session<br/>*   TS: Shared Table Lock<br/>*   TX: Exclusive Table Lock<br/>*   NONE: Waiting for Table Lock |