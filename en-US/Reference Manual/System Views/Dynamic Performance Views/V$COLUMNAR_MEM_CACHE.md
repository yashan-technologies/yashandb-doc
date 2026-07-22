This view displays the memory buffer information for columnar storage stable data.

|Field |Type |Description |
| --- | --- | --- |
| CACHE_ID   | INTEGER  | Buffer block number                  |
| QUEUE_ID   | SMALLINT | Queue number where the buffer block is located |
| DATAOBJ    | BIGINT   | Data object ID of the buffer object  |
| SLICE_ID   | BIGINT   | Slice number of the buffer object    |
| COLUMN_ID  | SMALLINT | Column number of the buffer object    |
| IS_META    | BOOLEAN  | Whether the buffer object is metadata |
| FLAG       | SMALLINT | Buffer identifier                     |
| RECENT_HITS| INTEGER  | Recently hit count of the buffer object |
| REFCOUNT   | INTEGER  | Reference count of the buffer object  |
| OBJSIZE    | INTEGER  | Size of the buffer object (unit: bytes) |
| QUERY_DIGEST | BIGINT | Statement fingerprint for loading the buffer object |