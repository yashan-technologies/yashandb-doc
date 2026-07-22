This view shows the compression information of all LSC tables (partitions) listed below.

|Field |Type |Description |
|--------------------------|-------------|----------------------------|
| OWNER                    | VARCHAR(64) | The username of the LSC table  |
| TABLESPACE\_NAME         | VARCHAR(64) | The name of the tablespace where the LSC is located |
| TABLE\_NAME              | VARCHAR(64) | The name of the LSC table      |
| SUB\_NAME                | VARCHAR(64) | If it is a partition table, it indicates the name of the partition table |
| COLUMN\_ID               | SMALLINT    | Column ID                      |
| ORIGINAL\_SIZE           | NUMBER      | The original input data size   |
| DATA\_ENCODED\_SIZE      | NUMBER      | The size of the data before encoding and compression (excluding metadata) |
| DATA\_COMPRESSED\_SIZE   | NUMBER      | The size of the data file after encoding and compression (excluding metadata file) |
| META\_COMPRESSED\_SIZE   | NUMBER      | The size of the metadata file after encoding and compression (excluding data file) |
| COMPRESSED\_SIZE         | NUMBER      | The size of the data after encoding and compression (including data file and metadata file) |
| DATA\_COMPRESSION\_RATIO | NUMBER      | The compression percentage of the data file (excluding metadata file) |
| COMPRESSION\_RATIO       | NUMBER      | The compression percentage of all data (including data file and metadata file) |