This view shows the compression information of all LSC tables (partitions) listed below.

|Field |Type |Description |
|--------------------------|-------------|----------------------------|
| OWNER                    | VARCHAR(64) | The username of the LSC table  |
| TABLESPACE_NAME         | VARCHAR(64) | The name of the tablespace where the LSC is located |
| TABLE_NAME              | VARCHAR(64) | The name of the LSC table      |
| SUB_NAME                | VARCHAR(64) | If it is a partition table, it indicates the name of the partition table |
| COLUMN_ID               | SMALLINT    | Column ID                      |
| ORIGINAL_SIZE           | NUMBER      | The original input data size   |
| DATA_ENCODED_SIZE      | NUMBER      | The size of the data before encoding and compression (excluding metadata) |
| DATA_COMPRESSED_SIZE   | NUMBER      | The size of the data file after encoding and compression (excluding metadata file) |
| META_COMPRESSED_SIZE   | NUMBER      | The size of the metadata file after encoding and compression (excluding data file) |
| COMPRESSED_SIZE         | NUMBER      | The size of the data after encoding and compression (including data file and metadata file) |
| DATA_COMPRESSION_RATIO | NUMBER      | The compression percentage of the data file (excluding metadata file) |
| COMPRESSION_RATIO       | NUMBER      | The compression percentage of all data (including data file and metadata file) |