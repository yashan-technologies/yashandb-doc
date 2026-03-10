This view is used to detect the tablespace of temporary attributes and its extent allocation status.  
|Field |Type |Description |
| --- | --- | --- |
| TABLESPACE_NAME  | VARCHAR(64)    | Tablespace name           |  
| FILE_ID          | INTEGER        | Data file global ID       |  
| EXTENTS_CACHED   | NUMBER         | Number of extents buffered |  
| EXTENTS_USED     | NUMBER         | Number of extents used    |  
| BLOCKS_CACHED    | NUMBER         | Number of blocks buffered  |  
| BLOCKS_USED      | NUMBER         | Number of blocks used      |  
| BYTES_CACHED      | NUMBER         | Number of bytes buffered   |  
| BYTES_USED       | NUMBER         | Number of bytes used       |  
| INTER_FNO       | INTEGER        | File ID of data file in the tablespace |