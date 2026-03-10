This view shows the free space information of data files under all tablespaces.

|Field |Type |Description |
| --- | --- | --- |
| TABLESPACE_NAME  | VARCHAR(64) | Tablespace name |
| FILE_ID | INTEGER | Global ID of the data file |
| BLOCK_ID | INTEGER | Starting block number of the free EXTENT |
| BYTES  | BIGINT | Size of the free EXTENT (unit: bytes) |
| BLOCKS | INTEGER | Size of the free EXTENT (unit: pages) |
| RELATIVE_FNO | INTEGER | Sequence number of the data file within the tablespace |