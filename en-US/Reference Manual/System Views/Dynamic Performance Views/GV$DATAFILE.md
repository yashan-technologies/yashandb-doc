This view displays summary information of data files.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | NUMBER | Group ID |
| GROUP_NODE_ID | NUMBER | Node ID within the group |
| INST_ID | NUMBER | Instance ID |
| ID  | INTEGER | ID number of Data file |
| NAME | VARCHAR(255) | Name of Data file |
| CREATION\_TIME | TIMESTAMP(6) | Creation time of Data file |
| TS# | INTEGER | ID number of the tablespace to which the Data file belongs |
| BLOCKS | INTEGER | Number of blocks in the Data file |
| BLOCK\_SIZE | INTEGER | Size of the Data file's data block (unit: bytes) |
| BYTES | BIGINT | Size of the Data file (unit: bytes) |
| STATUS | VARCHAR(8) | The Data file can have online and offline statuses; offline Data files are unavailable |
| RELATIVE\_FNO | INTEGER | ID number of the Data file within the tablespace |
| AUTO\_EXTEND | VARCHAR(8) | Whether the Data file is set to auto-extend |
| NEXT\_SIZE | BIGINT | Size of each extension for the Data file (unit: bytes) |
| MAX\_SIZE | BIGINT | Maximum size to which the Data file can extend (unit: bytes) |
| FREE\_BLOCKS | INTEGER | Number of free and available data blocks in the Data file |
| DISK\_BYTES | BIGINT | Physical disk space occupied (unit: bytes) |
| SHADOW | VARCHAR(8) | Indicates whether the target temporary file is in shadow status; TRUE means it is a shadow file, FALSE means it is a normal file<br/>If the standby database is promoted, the temporary file will be unusable due to its shadow status, and new temporary files must be added manually for use.<br/>To delete a shadow file using DROP TEMPFILE, ensure the database that created the temporary file is the current primary database (if not, switch to the primary database first) and execute the DROP TEMPFILE statement on that primary database. Other standby databases will clean up the shadow record only after receiving the redo for DROP TEMPFILE. |
| FILE#  | INTEGER | Number of the Data file |