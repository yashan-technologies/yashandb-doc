This view displays summary information about the current instance status.

|Field |Type |Description |
| --- |--------------| --- |
| STATUS              | VARCHAR(12)    | Instance status<br/>* CLOSED: The database process is starting<br/>* STARTED: The database process has started; operations on the database are not allowed in this status<br/>* MOUNTED: The database process has loaded physical files; limited maintenance operations are allowed in this status<br/>* OPEN: The database is operating normally<br/>* OPEN UPGRADE: The database has entered upgrade mode |
| VERSION             | VARCHAR(64)    | Database version number |
| STARTUP_TIME        | TIMESTAMP      | Database instance startup time |
| HOST_NAME           | VARCHAR(256)   | Server username |
| DATA_HOME           | VARCHAR(256)   | Database DATA path |
| INSTANCE_NUMBER     | INTEGER        | Instance number |
| INSTANCE_NAME       | VARCHAR(64)    | Instance name |
| PARALLEL            | BOOLEAN        | Whether the instance is mounted in cluster database mode (YES) or (NO) |
| INSTANCE_ROLE       | VARCHAR(64)    | Current instance role<br/>* MASTER_ROLE: The current instance is MASTER<br/>* NORMAL_ROLE: The current instance is NORMAL<br/> This field is only meaningful in YAC Deployment; in other deployment modes, the value of this field is always MASTER_ROLE |
| IN_REFORM           | VARCHAR(8)     | Whether the cluster instance is processing joining, leaving, or fault recovery<br/> This field is only meaningful in YAC Deployment; in other deployment modes, the value of this field is always NO |
| IGNORE_OPEN         | BOOLEAN        | Whether the instance has ignored the OPEN startup parameter and started in NOMOUNT mode<br/> This field is only meaningful in YAC primary/standby deployments; it is TRUE only when a standby cluster non-MASTER instance starts in OPEN mode |
| DATABASE_STATUS     | VARCHAR(17)    | Status of the database<br/>* ACTIVE: Operating normally<br/>* INSTANCE RECOVERY: The database is performing instance recovery (in YACs, when instances are leaving or joining) |