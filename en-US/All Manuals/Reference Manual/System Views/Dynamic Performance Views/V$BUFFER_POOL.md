This view shows the basic information of data buffer.

|Field |Type |Description |
| --- | --- | --- |
| ID     | INTEGER | Buffer partition number                       |
| SIZE   | BIGINT  | Buffer partition size (unit: bytes)          |
| BLOCK_SIZE | INTEGER | The size of data block in the buffer partition (unit: bytes) |
| CHUNKS | INTEGER | The number of data chunks in the data block partition |
| BUFFERS | INTEGER | The number of data blocks in the data block partition |