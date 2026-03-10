This view displays all tablespaces that belong to the tablespace set and is applicable only for ISC Distributed Cluster Deployment.

|Field |Type |Description |
| --- |-------------|-------------------------------------------------------------------------------------|
| TABLESPACE_NAME     | VARCHAR(64)   | Tablespace name                                                                             |
| TABLESPACE_ID       | INTEGER       | Tablespace ID                                                                               |
| TABLESPACE_SET_NAME | VARCHAR(64)   | Name of the belonging tablespace set                                                        |
| CHUNK_ID            | INTEGER       | Chunk ID corresponding to the tablespace                                                    |
| RESIDUAL            | VARCHAR(8)    | Indicates whether it is an old tablespace left over from redistribution. TRUE means it is an old tablespace from redistribution (i.e., the current tablespace has been migrated to another node) |