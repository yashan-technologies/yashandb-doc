This view displays archived slice file index information, used to view the archived slice file index status.

| Field          | Type         | Description                                    |
|----------------|--------------|------------------------------------------------|
| DATA_OID       | BIGINT       | DATA OID of the archived slice file            |
| SLICE_ID       | BIGINT       | ID of the archived slice file                  |
| LFN            | BIGINT       | Creation LFN of the archived slice file        |
| BUCKET_ID      | INTEGER      | BUCKET ID of the record in the archived slice file |
| BUCKET_PATH    | VARCHAR(255) | BUCKET of the record in the archived slice file |
| COLUMN_ID      | SMALLINT     | Column ID of the archived slice file           |
| ARCH_INST_ID   | TINYINT      | Creating instance ID of the archived slice file |
| HAS_CLEAN      | BOOLEAN      | Whether the archived slice file has been cleaned |
| SIZE           | BIGINT       | Size of the archived slice file                |