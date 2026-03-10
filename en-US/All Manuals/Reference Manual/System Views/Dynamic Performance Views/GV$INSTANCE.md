This view displays a summary of the status of all instances in the cluster.

|Field |Type |Description |
| --- |--------------| --- |
| GROUP_ID            | NUMBER         | Group ID     |
| GROUP_NODE_ID       | NUMBER         | Node ID within the group |
| INST_ID             | NUMBER         | Instance ID  |
| STATUS              | VARCHAR(12)    | Instance status<br/>* CLOSED: The database process is starting<br/>* STARTED: The database process has started, operations on the database are not allowed in this state<br/>* MOUNTED: The database process has loaded physical files, minimal maintenance operations are allowed in this state<br/>* OPEN: The database is in normal operating state<br/>* OPEN UPGRADE: The database has entered upgrade mode |
| VERSION             | VARCHAR(64)    | Database version number |
| STARTUP_TIME        | TIMESTAMP      | Database instance startup time |
| HOST_NAME           | VARCHAR(256)   | Server username |
| DATA_HOME           | VARCHAR(256)   | Database DATA path |
| INSTANCE_NUMBER     | INTEGER        | Instance number |
| INSTANCE_NAME       | VARCHAR(64)    | Instance name |
| PARALLEL            | BOOLEAN        | Whether the instance is mounted in cluster database mode (YES) or (NO) |
| INSTANCE_ROLE       | VARCHAR(64)    | Current instance role <br/>* MASTER_ROLE: Current instance is MASTER <br/>* NORMAL_ROLE: Current instance is NORMAL |
| IN_REFORM           | VARCHAR(8)     | Whether it is processing cluster instance addition, removal, or fault recovery |
| IGNORE_OPEN         | BOOLEAN        | Whether the instance has ignored the OPEN startup parameter and actually started in NOMOUNT mode <br/> This field is meaningful only in YAC primary/standby deployments, and is TRUE when a standby cluster non-MASTER instance is started in OPEN mode |
| DATABASE_STATUS     | VARCHAR(17)    | Database status <br/>* ACTIVE: Running normally <br/>* INSTANCE RECOVERY: The database is undergoing instance recovery (in YACs, when an instance is joining or leaving) |