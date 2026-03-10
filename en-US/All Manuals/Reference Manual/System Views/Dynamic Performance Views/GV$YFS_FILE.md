This view displays information about YFS files (File).

|Field |Type |Description |
|----------------|-------------|----------------------------------------------|
| GROUP_ID         | NUMBER       | Group ID                                     |
| GROUP_NODE_ID    | NUMBER       | Node ID within the group                      |
| INST_ID          | NUMBER       | Instance ID                                  |
| FILE_NUMBER      | INTEGER      | File number within the DiskGroup             |
| COMPOUND_INDEX   | INTEGER      | File handle containing Diskgroup ID          |
| GROUP_NUMBER     | INTEGER      | ID of the associated Diskgroup                |
| BLOCK_SIZE       | INTEGER      | File block size, which is the AU Size of the Diskgroup |
| BLOCKS           | BIGINT       | Number of Blocks in the file                  |
| BYTES            | BIGINT       | Byte count of the file                        |
| REDUNDANCY       | VARCHAR(16)  | File redundancy <br/>* EXTERNAL: No mirror<br/>* NORMAL: Two mirrors<br/>* HIGH: Three mirrors |
| CREATION_DATE    | DATE         | Creation date                                 |
| DELETE_TIME      | DATE         | Deletion date                                 |
| TYPE             | INTEGER      | File type number, above 200 is a metadata type, the type number is specified by DB |