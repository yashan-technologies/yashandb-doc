This view shows information about all tablespace collections and is applicable only to ISC Distributed Cluster Deployment.

|Field |Type |Description |
|-------------------------|--------------|------------------------------------------------------|
| GROUP_ID               | NUMBER       | Group ID                                             |
| GROUP_NODE_ID          | NUMBER       | Node ID within the group                             |
| INST_ID                | NUMBER       | Instance ID                                          |
| OID                    | BIGINT       | OID of the tablespace collection                     |
| NAME                   | VARCHAR(64)  | Name of the tablespace collection                    |
| DS_ID                  | BIGINT       | ID of the associated dataspace                        |
| NEXT_BLOCKS           | INTEGER      | Number of data blocks for a single expansion of the data file in this tablespace collection |
| BLOCK_SIZE             | INTEGER      | Size of the data block in the data file (unit: bytes) |
| DATAFILES_COUNT        | NUMBER(38)       | Total number of data files in this tablespace collection |
| DATAFILES_MAX_SIZE     | NUMBER       | Total maximum size of data files in this tablespace collection on the current node |
| DATAFILES_SIZE         | NUMBER       | Total actual size of all data files in this tablespace collection |
| DATAFILES_FREE_BLOCKS   | NUMBER       | Total number of free available data blocks in all data files within this tablespace collection |
| DATABUCKETS_COUNT      | NUMBER(38)       | Total number of DATABUCKET files in this tablespace collection |
| DATABUCKETS_MAX_SIZE   | NUMBER       | Total maximum size of all DATABUCKET files in this tablespace collection on the current node |
| DATABUCKETS_SIZE       | NUMBER       | Total actual size of all databuckets in this tablespace collection |