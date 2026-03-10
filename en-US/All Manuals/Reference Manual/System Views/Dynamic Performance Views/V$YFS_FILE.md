This view displays information about YFS files.

|Field |Type |Description |
|-------|------|------|
| FILE_NUMBER    | INTEGER  | File number within the DiskGroup          |
| COMPOUND_INDEX | INTEGER  | File handle containing Diskgroup ID       |
| GROUP_NUMBER   | INTEGER  | ID of the owning Diskgroup                 |
| BLOCK_SIZE     | INTEGER  | File block size, i.e., the AU Size of the Diskgroup |
| BLOCKS         | BIGINT   | Number of blocks in the file               |
| BYTES          | BIGINT   | Number of bytes in the file                |
| REDUNDANCY     | VARCHAR(16)| File redundancy <br/>* EXTERNAL: No mirroring<br/>* NORMAL: Two copies of mirroring<br/>* HIGH: Three copies of mirroring |
| CREATION_DATE  | DATE     | Creation date                              |
| DELETE_TIME    | DATE     | Deletion date                             |
| TYPE           | INTEGER  | File type number, with numbers above 200 indicating metadata types; type numbers are designated by the DB |