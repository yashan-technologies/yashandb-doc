This view displays information about YFS files.

|Field |Type |Description |
|----------------|-------------|----------------------------------------------|
| GROUP_ID         | NUMBER       | Group ID                                     |
| GROUP_NODE_ID    | NUMBER       | Node ID within the group                      |
| INST_ID          | NUMBER       | Instance ID                                  |
|   FILE_NUMBER    | INTEGER  | File number within the DiskGroup          |
| COMPOUND_INDEX | INTEGER  | File handle containing DiskGroup ID       |
| GROUP_NUMBER   | INTEGER  | The ID of the DiskGroup where the file resides.              |
| BLOCK_SIZE     | INTEGER  | The block size of the file, which is the AU_Size of the DiskGroup it resides in |
| BLOCKS         | BIGINT   | The number of blocks in the file               |
| BYTES          | BIGINT   | The byte size of the file  |
| REDUNDANCY     | VARCHAR(16)| File redundancy <br/>* EXTERNAL: No replicas <br/>* NORMAL: 2 replicas    <br/>* HIGH: 3 replicas |
| CREATION_DATE  | DATE     | The creation date of the file         |
| DELETE_TIME    | DATE     | The deletion date of the file           |
| TYPE           | INTEGER  | The file type ID; IDs above 200 represent YFS metadata types, which are specified by the database  |
