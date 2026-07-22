This view shows the free space information of data files under all tablespaces.

- If a data file (or tablespace) is completely offline, this view will not record information about that data file (or tablespace).
- If a database object occupies some extents in an online tablespace file and also occupies some extents in an offline file, this view will additionally record the free extent information for those offline portions.
- If a data file is completely filled (no available space), this view will not record information about that data file.

|Field |Type |Description |
| --- | --- | --- |
| TABLESPACE\_NAME  | VARCHAR(64) | Tablespace name |
| FILE\_ID | INTEGER | Global ID of the data file |
| BLOCK\_ID | INTEGER | Starting block number of the free EXTENT |
| BYTES  | BIGINT | Size of the free EXTENT (unit: bytes) |
| BLOCKS | INTEGER | Size of the free EXTENT (unit: pages) |
| RELATIVE\_FNO | INTEGER | Sequence number of the data file within the tablespace |