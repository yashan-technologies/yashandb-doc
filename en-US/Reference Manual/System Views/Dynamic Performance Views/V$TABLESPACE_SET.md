This view displays all tablespace set information for the current node, applicable only to ISC Distributed Cluster Deployment.

|Field |Type |Description |
|-------------------------|--------------|------------------------------------------------------|
| OID                     | BIGINT       | OID of the tablespace set                            |
| NAME                    | VARCHAR(64)  | Name of the tablespace set                           |
| DS_ID                   | BIGINT       | ID of the associated dataspace                        |
| NEXT_BLOCKS            | INTEGER      | Number of data blocks for a single expansion of the data file in this tablespace set |
| BLOCK_SIZE              | INTEGER      | Size of data blocks in the data file (unit: bytes)  |
| DATAFILES_COUNT         | NUMBER       | Total number of data files in this tablespace set    |
| DATAFILES_MAX_SIZE      | NUMBER       | Total maximum SIZE of data files in this tablespace set |
| DATAFILES_SIZE          | NUMBER       | Total actual size of all data files in this tablespace set |
| DATAFILES_FREE_BLOCKS    | NUMBER       | Total number of free available data blocks in all data files of this tablespace set |
| DATABUCKETS_COUNT       | NUMBER       | Total number of DATABUCKET files in this tablespace set |
| DATABUCKETS_MAX_SIZE    | NUMBER       | Total maximum SIZE of all DATABUCKET files on the current node for this tablespace set |
| DATABUCKETS_SIZE        | NUMBER       | Total actual size of all databuckets in this tablespace set |