This view displays information about all PDBs in the current instance in the cluster.

|Field |Type |Description |
|---------------|--------------|--------------------|
| GROUP_ID      | NUMBER       | Group ID               |
| GROUP_NODE_ID | NUMBER       | Node ID in current group      |
| INST_ID       | NUMBER       | Instance ID                   |
| CON_ID        | BIGINT       | Container ID: <br/> * 0: This value is used only for the CDB root (CDB$ROOT)<br/> * 1: This value is used only for the PDB seed (PDB$SEED)<br/> * n: Where n is the standard PDB ID             |
| OID           | BIGINT       | The ID of the object to which the container belongs               |
| CON_UID       | VARCHAR(33)  | Unique identifier associated with the PDB                  |
| NAME          | VARCHAR(68)  | Name of the PDB                        |
| TYPE          | VARCHAR(16)  | The container type, including: <br/> * ROOT: The CDB root<br/> * ​​SEED​​: The seed PDB used as a template <br/> * ​​PDB​​: A pluggable database         |
| STATUS        | VARCHAR(16)  | The status of the container：<br/> * CLOSED：The container process has been terminated.<br/> * STARTED：The container process is already running. Database operations cannot be performed in this state.<br/> * MOUNTED：The container process has loaded the physical files. In this state, only limited maintenance operations can be performed.<br/> * OPEN：The container is running normally. |
| FLAGS         | TINYINT      | The flags of the container                   |
| HOME          | VARCHAR(256) | The DATA directory of the container, storing its data files              |
| COMPAT_MODE | VARCHAR(16) | Syntax mode of container instances: <br/>* YASHAN：yashan mode<br/>* MYSQL：mysql mode |