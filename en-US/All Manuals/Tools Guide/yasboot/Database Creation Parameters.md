This article describes the database configuration parameters at the group level.

> **Caution**: 
>
> - Group configuration can only be used in the parameters configuration of the [CREATE DATABASE](../../Development Guide/SQL Reference Manual/SQL Statements/CREATE DATABASE) statement, meaning only the configurations listed below can be used, and there cannot be any database configuration parameters on the nodes.
> - All configurations are optional, and default values will be used if not configured.
> - An error will occur if the configuration item does not exist in the list below.
> - The actual effective values after deployment depend on the server's configuration.
> - In ISC Distributed Cluster Deployment, the USERS_DATASPACE_SCALE_OUT_FACTOR parameter determines the initial number of shards for each DN group. Increasing this parameter value appropriately benefits the parallel query threads to enhance query efficiency, but it will also increase the memory overhead when importing data and prolong the data migration time.

**Alphabetical Index**

[c](#cpara)		[d](#dpara)		[i](#ipara)		[m](#mpara)		[r](#rpara)		[s](#spara)		[t](#tpara)		[u](#upara)

<span id="cpara" name="cpara" class="yaslink"></span>

### CTRL\_FILE\_NUM

* Parameter Type: Numeric

* Default Value: 3

* Value Range/Format: \[2,8\]

* Parameter Description: Controls the number of control files. This parameter is not effective in YAC Deployment.

### CHARACTER\_SET

* Parameter Type: String

* Default Value: UTF8

* Value Range/Format: ASCII, ISO88591, GBK, UTF8, GB18030

* Parameter Description: Specifies the character set used by the database.

<span id="dpara" name="dpara" class="yaslink"></span>

### DATA\_FILE\_INIT\_SIZE

* Parameter Type: Numeric

* Default Value: 64M

* Value Range/Format: \[8M,512G\]

* Parameter Description: Default initialization size of the data tablespace.

### DATA\_FILE\_MAX\_SIZE

* Parameter Type: Numeric

* Default Value: 512G

* Value Range/Format: \[8M,512G\]

* Parameter Description: Maximum limit of the default data tablespace.

### DATA\_FILE\_NUM

* Parameter Type: Numeric

* Default Value: 1

* Value Range/Format: \[1,64\]

* Parameter Description: Number of data files in the default data tablespace.

### DOUBLE\_WRITE\_FILE\_SIZE

* Parameter Type: Numeric

* Default Value: 64M

* Value Range/Format: \[16M,4096M\]

* Parameter Description: Size of the double write file.

<span id="ipara" name="ipara" class="yaslink"></span>

### ISARCHIVELOG

* Parameter Type: Boolean

* Default Value: true

* Value Range/Format: true/false

* Parameter Description: Whether to enable database archiving mode.

<span id="mpara" name="mpara" class="yaslink"></span>

### MMS\_TABLESPACE\_SET\_SIZE

* Parameter Type: Numeric

* Default Value: 300M

* Value Range/Format: \[64M,1500G\]

* Parameter Description: Size of the built-in MMS tablespace set, effective only on CN nodes in ISC Distributed Cluster Deployment (i.e., configured in the group.config of the CN group).

### MMS\_DATA\_FILE\_NUM

* Parameter Type: Numeric

* Default Value: 1

* Value Range/Format: \[1,64\]

* Parameter Description: Number of files in the built-in MMS tablespace, effective only on CN nodes in ISC Distributed Cluster Deployment (i.e., configured in the group.config of the CN group).

### MMS\_DATA\_FILE\_SIZE

* Parameter Type: Numeric

* Default Value: 32M

* Value Range/Format: \[8M,512G\]

* Parameter Description: Size of each file in the built-in MMS tablespace, effective only on CN nodes in ISC Distributed Cluster Deployment (i.e., configured in the group.config of the CN group).

<span id="rpara" name="rpara" class="yaslink"></span>

### REDO\_BLOCK\_SIZE

* Parameter Type: Numeric

* Default Value: 4K for Standalone Deployment and ISC Distributed Cluster Deployment, 512 for YAC Deployment

* Value Range/Format: \[512,32K\]

* Parameter Description: Size of the redo block.

### REDO\_FILE\_NUM

* Parameter Type: Numeric

* Default Value: 3

* Value Range/Format: \[3,256\]

* Parameter Description: Number of redo files.

### REDO\_FILE\_PATH

* Parameter Type: String

* Default Value: ?/dbfiles

* Value Range/Format: If this parameter is configured, it must be set to a standard directory path format (absolute path).

* Parameter Description: Path of the redo files; this parameter cannot be modified in YAC Deployment.

### REDO\_FILE\_SIZE

* Parameter Type: Numeric

* Default Value: 128M

* Value Range/Format: \[100M,2T\]

* Parameter Description: Size of the redo files.

<span id="spara" name="spara" class="yaslink"></span>

### SWAP\_FILE\_INIT\_SIZE

* Parameter Type: Numeric

* Default Value: 64M for Standalone Deployment and ISC Distributed Cluster Deployment, 128M for YAC Deployment

* Value Range/Format: \[8M,512G\]

* Parameter Description: Initialization size of the swap tablespace.

### SWAP\_FILE\_MAX\_SIZE

* Parameter Type: Numeric

* Default Value: 512G for Standalone Deployment and ISC Distributed Cluster Deployment, 64G for YAC Deployment

* Value Range/Format: \[8M,512G\]

* Parameter Description: Maximum limit of the swap tablespace.

### SWAP\_FILE\_NUM

* Parameter Type: Numeric

* Default Value: 1

* Value Range/Format: \[1,64\]

* Parameter Description: Number of data files in the swap tablespace.

### SYSAUX\_FILE\_INIT\_SIZE

* Parameter Type: Numeric

* Default Value: 64M for Standalone Deployment and ISC Distributed Cluster Deployment, 128M for YAC Deployment

* Value Range/Format: \[8M,512G\]

* Parameter Description: Initialization size of the system auxiliary tablespace.

### SYSAUX\_FILE\_MAX\_SIZE

* Parameter Type: Numeric

* Default Value: 512G for Standalone Deployment and ISC Distributed Cluster Deployment, 64G for YAC Deployment

* Value Range/Format: \[8M,512G\]

* Parameter Description: Maximum limit of the system auxiliary tablespace.

### SYSAUX\_FILE\_NUM

* Parameter Type: Numeric

* Default Value: 1

* Value Range/Format: \[1,64\]

* Parameter Description: Number of data files in the system auxiliary tablespace.

### SYSTEM\_FILE\_INIT\_SIZE

* Parameter Type: Numeric

* Default Value: 64M for Standalone Deployment and ISC Distributed Cluster Deployment, 128M for YAC Deployment

* Value Range/Format: \[8M,512G\]

* Parameter Description: Initialization size of the system tablespace.

### SYSTEM\_FILE\_MAX\_SIZE

* Parameter Type: Numeric

* Default Value: 512G for Standalone Deployment and ISC Distributed Cluster Deployment, 64G for YAC Deployment

* Value Range/Format: \[32M,512G\]

* Parameter Description: Maximum limit of the system tablespace.

### SYSTEM\_FILE\_NUM

* Parameter Type: Numeric

* Default Value: 1

* Value Range/Format: \[1,64\]

* Parameter Description: Number of data files in the system tablespace.

<span id="tpara" name="tpara" class="yaslink"></span>

### TEMP\_FILE\_INIT\_SIZE

* Parameter Type: Numeric

* Default Value: 64M for Standalone Deployment and ISC Distributed Cluster Deployment, 128M for YAC Deployment

* Value Range/Format: \[8M,512G\]

* Parameter Description: Initialization size of the temporary tablespace.

### TEMP\_FILE\_MAX\_SIZE

* Parameter Type: Numeric

* Default Value: 512G for Standalone Deployment and ISC Distributed Cluster Deployment, 64G for YAC Deployment

* Value Range/Format: \[8M,512G\]

* Parameter Description: Maximum limit of the temporary tablespace.

### TEMP\_FILE\_NUM

* Parameter Type: Numeric

* Default Value: 1

* Value Range/Format: \[1,64\]

* Parameter Description: Number of data files in the temporary tablespace.

### TIME\_ZONE

* Parameter Type: String

* Default Value: ""

* Value Range/Format: \[-15:59, +15:59\]

* Parameter Description: Time zone.

<span id="upara" name="upara" class="yaslink"></span>

### UNDO\_FILE\_INIT\_SIZE

* Parameter Type: Numeric

* Default Value: 128M for Standalone Deployment and ISC Distributed Cluster Deployment, 192M for YAC Deployment

* Value Range/Format: \[8M,512G\]

* Parameter Description: Initialization size of the undo tablespace.

### UNDO\_FILE\_MAX\_SIZE

* Parameter Type: Numeric

* Default Value: 64G

* Value Range/Format: \[8M,512G\]

* Parameter Description: Maximum limit of the undo tablespace.

### UNDO\_FILE\_NUM

* Parameter Type: Numeric

* Default Value: 1

* Value Range/Format: \[1,64\]

* Parameter Description: Number of data files in the undo tablespace.

### UNDO\_SEGMENTS

* Parameter Type: Numeric

* Default Value: 64

* Value Range/Format: \[8,1024\]

* Parameter Description: Maximum number of rollback segments that the database can automatically extend; this parameter is not effective in YAC Deployment.

### USERS\_DATASPACE\_SCALE\_OUT\_FACTOR

* Parameter Type: Numeric

* Default Value: 32

* Value Range/Format: \[1,128\]

* Parameter Description: Number of shards for the USERS data space for each DN group in the cluster. The number of shards specified when creating the USERS data space is the product of this parameter value and the number of DN groups in the cluster. Effective only on CN nodes in ISC Distributed Cluster Deployment (i.e., configured in the group.config of the CN group).

### USERS\_TABLESPACE\_SET\_CHUNK\_SIZE

* Parameter Type: Numeric

* Default Value: 5M

* Value Range/Format: \[1M,32T\]

* Parameter Description: Initial size of the data file for each shard of the USERS tablespace set, effective only on CN nodes in ISC Distributed Cluster Deployment (i.e., configured in the group.config of the CN group).

### USERS\_TABLESPACE\_SET\_CHUNK\_MAX\_SIZE

* Parameter Type: Numeric

* Default Value: 1T

* Value Range/Format: \[1M,32T\]

* Parameter Description: Maximum expansion space for each shard of the USERS tablespace set, effective only on CN nodes in ISC Distributed Cluster Deployment (i.e., configured in the group.config of the CN group).

### USERS\_TABLESPACE\_SET\_NEXT\_SIZE

* Parameter Type: Numeric

* Default Value: 64M

* Value Range/Format: \[4M,256M\]

* Parameter Description: Size of each automatic expansion for the data files within the USERS tablespace set, effective only on CN nodes in ISC Distributed Cluster Deployment (i.e., configured in the group.config of the CN group).