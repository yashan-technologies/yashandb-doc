This view displays information about PAST COPY BLOCK (when multiple instances concurrently hold dirty blocks, only the latest version of one instance possesses write privilege, while the dirty blocks held by historical version instances that do not possess write privilege are referred to as PAST COPY BLOCK). 

|Field |Type |Description |
| --- | --- | --- |
| TS#              | INTEGER   | Page space id                                                   |
| FILE#            | INTEGER   | Page file id                                                    |
| BLK#             | INTEGER   | Page ID                                                         |
| RESOURCE_NAME    | VARCHAR(128) | Resource name, block resource \[space\]\[file\]\[id\]       |
| INSTANCE_ID      | TINYINT   | Node holding the PAST COPY BLOCK                                |
| LSN              | BIGINT    | LSN (Log Sequence Number) of the PAST COPY BLOCK               |