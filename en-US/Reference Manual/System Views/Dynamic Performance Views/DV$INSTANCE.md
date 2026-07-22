This view displays summary information about the status of all node instances in the ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP\_ID | INTEGER | Group ID |
| GROUP\_NODE\_ID | INTEGER | Node ID within the group |
| STATUS | VARCHAR(8) | Instance status<br>\* CLOSED: The database process is starting<br>\* STARTED: The database process has started, and no database operations can be performed in this state<br>\* MOUNTED: The database process has loaded physical files, and limited maintenance operations can be performed in this state<br>\* OPEN: The database is running normally<br>\* OPEN UPGRADE: The database has entered upgrade mode |
| VERSION | VARCHAR(64) | Database version number |
| STARTUP\_TIME | TIMESTAMP(6) | Database instance startup time |
| HOST\_NAME | VARCHAR(256) | Server username |
| DATA\_HOME | VARCHAR(256) | Database DATA path |
| INSTANCE_ROLE | VARCHAR(16) | Current instance role <br/>* MASTER_ROLE: The current instance is MASTER <br/>* NORMAL_ROLE: The current instance is NORMAL |