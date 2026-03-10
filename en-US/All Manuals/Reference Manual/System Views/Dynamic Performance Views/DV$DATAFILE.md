This view displays the summary information of data files for all nodes in the ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID              | INTEGER  | Group ID                                                                                                             |
| GROUP_NODE_ID         | INTEGER  | Node ID within the group                                                                                             |
| ID                    | INTEGER  | ID number of the data file                                                                                            |
| NAME                  | VARCHAR(255) | Name of the data file                                                                                               |
| CREATION_TIME         | TIMESTAMP | Creation time of the data file                                                                                        |
| TS#                   | INTEGER  | ID number of the tablespace to which the data file belongs                                                          |
| BLOCKS                | INTEGER  | Number of blocks in the data file                                                                                    |
| BLOCK_SIZE            | INTEGER  | Size of the data block for the data file (unit: bytes)                                                              |
| BYTES                 | BIGINT   | Size of the data file (unit: bytes)                                                                                 |
| STATUS                | VARCHAR(8) | The data file can be in online or offline status; offline data files are not available                               |
| RELATIVE_FNO         | INTEGER  | ID number of the data file within the tablespace                                                                     |
| AUTO_EXTEND           | VARCHAR(8) | Whether the data file is set to auto-extend                                                                         |
| NEXT_SIZE             | BIGINT   | Size of the data file for each extension (unit: bytes)                                                              |
| MAX_SIZE              | BIGINT   | Maximum size to which the data file can be extended (unit: bytes)                                                   |
| FREE_BLOCKS           | INTEGER  | Number of free and available data blocks within the data file                                                       |
| DISK_BYTES            | BIGINT   | Size occupied on the physical disk (unit: bytes)                                                                    |
| SHADOW                | VARCHAR(8) | Indicates whether the target temporary file is in shadow state; TRUE means it is a shadow file, FALSE means it is a normal file<br/>If the standby database becomes primary, the temporary file in shadow state cannot be used, and a new temporary file must be manually added for use.<br/>To delete the shadow file using DROP TEMPFILE, ensure that the database that created the temporary file is the current primary database (if not, switch to the primary database first) and execute the DROP TEMPFILE statement on that primary database; other standby databases will clean up the shadow records after receiving the redo for DROP TEMPFILE. |