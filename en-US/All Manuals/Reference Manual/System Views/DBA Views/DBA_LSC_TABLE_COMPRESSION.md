This view displays the compression information for all LSC tables (partitions).

|Field |Type |Description |
|--------------------------|-------------|----------------------------|
| OWNER                    | VARCHAR(64)   | The username of the owner of the LSC table    |
| TABLESPACE_NAME         | VARCHAR(64)   | The name of the tablespace where the LSC is located |
| TABLE_NAME              | VARCHAR(64)   | The name of the LSC table      |
| SUB_NAME                | VARCHAR(64)   | If it is a partition table, this indicates the name of the partition table |
| ROW_COUNT               | NUMBER        | The number of data rows        |
| ORIGINAL_SIZE           | NUMBER        | The size of the original input data |
| DATA_ENCODED_SIZE      | NUMBER        | The size of the data before compression (excluding metadata) |
| DATA_COMPRESSED_SIZE   | NUMBER        | The size of the data file after encoding compression (excluding the metadata file) |
| META_COMPRESSED_SIZE   | NUMBER        | The size of the metadata file after encoding compression (excluding the data file) |
| COMPRESSED_SIZE         | NUMBER        | The size of the data after encoding compression (including the data file and metadata file) |
| DATA_COMPRESSION_RATIO | NUMBER        | The compression percentage of the data file (excluding the metadata file) |
| COMPRESSION_RATIO       | NUMBER        | The overall compression percentage of all data (including the data file and metadata file) |