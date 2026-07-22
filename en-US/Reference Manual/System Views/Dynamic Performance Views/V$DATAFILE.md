This view displays the summary information of data files.

|Field |Type |Description |
| --- | --- | --- |
| ID             | INTEGER    | The ID number of the data file                                                                                |
| NAME           | VARCHAR(255) | The name of the data file                                                                                     |
| CREATION\_TIME | TIMESTAMP(6)  | The creation time of the data file                                                                             |
| TS#            | INTEGER    | The ID number of the tablespace to which the data file belongs                                                |
| BLOCKS         | INTEGER    | The number of blocks in the data file                                                                          |
| BLOCK\_SIZE    | INTEGER    | The size of the data block of the data file (unit: bytes)                                                    |
| BYTES          | BIGINT     | The size of the data file (unit: bytes)                                                                      |
| STATUS         | VARCHAR(8) | The data file can have online, offline, and recover states; offline and recover data files are not available, recover state needs to execute recover for recovery     |
| RELATIVE\_FNO  | INTEGER    | The ID number of the data file within the tablespace                                                         |
| AUTO\_EXTEND   | VARCHAR(8) | Whether the data file automatically extends                                                                    |
| NEXT\_SIZE     | BIGINT     | The size of each extension of the data file (unit: bytes)                                                    |
| MAX\_SIZE      | BIGINT     | The maximum expandable size of the data file (unit: bytes)                                                   |
| FREE\_BLOCKS   | INTEGER    | The number of free and available data blocks within the data file                                             |
| DISK\_BYTES    | BIGINT     | The physical disk space occupied (unit: bytes)                                                                |
| SHADOW         | VARCHAR(8) | Whether the target temporary file is in shadow state; TRUE indicates a shadow file, FALSE indicates a normal file<br/>If the standby database becomes primary, the temporary file is in shadow state and cannot be used, a new temporary file must be manually added for use.<br/>If you need to delete the shadow file using DROP TEMPFILE, ensure that the database that created the temporary file is the current primary database (if not, switch to the primary database first) and execute the DROP TEMPFILE statement on that primary database. Other standby databases will clear the shadow record after receiving the redo of the DROP TEMPFILE. |
| FILE#          | INTEGER    | The number of the data file                                                                                   |