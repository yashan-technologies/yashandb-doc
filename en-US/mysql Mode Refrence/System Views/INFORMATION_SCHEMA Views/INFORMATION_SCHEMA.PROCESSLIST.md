This view displays the session threads currently executing in the database service and the operations being performed.


|Field |Type |Description |
| --- | --- | --- |
| ID      | bigint unsigned |Session ID    |
| USER    | varchar(64)     |User who established the session   |
| HOST    | varchar(57)     |Client IP and port that established the session   |
| DB      | varchar(64)     |Database instance connected by the session   |
| COMMAND | varchar(16)     |Type of command being executed by the session or session state.<br>Query: The session is executing a query SQL statement<br>Daemon: Background resident<br>Sleep: The session is sleeping<br>Connect: The session is establishing a connection   |
| TIME    | int(38)         |Cumulative time the session has been in the current state, in seconds   |
| STATE   | varchar(28)     |Displays the execution status of the currently executing SQL statement   |
| INFO    | varchar(1000)   |Displays the currently executing SQL statement   |