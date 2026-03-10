This view displays summary information about the status of all node instances in the ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | INTEGER | Group ID |
| GROUP_NODE_ID | INTEGER | Node ID within the group |
| STATUS | VARCHAR(8) | Instance status<br>\* CLOSED: The database process is starting<br>\* STARTED: The database process has started, and no database operations can be performed in this state<br>\* MOUNTED: The database process has loaded physical files, and limited maintenance operations can be performed in this state<br>\* OPEN: The database is running normally<br>\* OPEN UPGRADE: The database has entered upgrade mode |
| VERSION | VARCHAR(64) | Database version number |
| STARTUP_TIME | TIMESTAMP(6) | Database instance startup time |
| HOST_NAME | VARCHAR(256) | Server username |
| DATA_HOME | VARCHAR(256) | Database DATA path |
| INSTANCE_ROLE | VARCHAR(16) | Current instance role <br/>* MASTER_ROLE: The current instance is MASTER <br/>* NORMAL_ROLE: The current instance is NORMAL |