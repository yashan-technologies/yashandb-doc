This view shows the data buffer page control information.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID       | NUMBER    | Group ID                                                  |
| GROUP_NODE_ID  | NUMBER    | Node ID within the group                                  |
| INST_ID        | NUMBER    | Instance ID                                               |
|  ADDR           | RAW(8)    | Memory address of buffer control                          |
| PART           | INTEGER   | Buffer control's buffer part area                        |
| ID             | INTEGER   | Buffer control number                                     |
| IN_OLD         | BOOLEAN   | Whether the buffer control is OLD                        |
| LIST_ID        | INTEGER   | LRU chain number                                         |
| HASH_NEXT      | INTEGER   | Next buffer control in the same bucket                   |
| CR_NEXT        | INTEGER   | Next CR buffer control                                    |
| LRU_NEXT       | INTEGER   | Next buffer control in the LRU chain                     |
| LRU_PREV       | INTEGER   | Previous buffer control in the LRU chain                 |
| BUCKET\_ID | INTEGER | BUCKET ID |
| TS#            | INTEGER   | Tablespace ID of the loaded buffer page                   |
| FILE#          | INTEGER   | File ID of the loaded buffer page                         |
| BLK#           | INTEGER   | Page ID of the loaded buffer page                         |
| DIRTY          | BOOLEAN   | Whether it is a dirty page                                 |
| LOAD_STATUS     | INTEGER   | Buffer page load status<br>\* 0: BP_NEED_LOAD, needs to read disk to load block<br>\* 1: BP_AIM_LOADING, currently loading aim block<br>\* 2: BP_IS_LOADED, block loading completed<br>\* 3: BP_LOAD_FAILED, last loading block failed<br>\* 4: BP_IS_RECYCLING, buffer control is in the elimination process |
| RES_STATUS     | INTEGER   | Resource status of buffer page in the cluster<br>\* 0: BP_RES_FREE, current Buffer Ctrl is idle<br>\* 1: BP_RES_CR, current Buffer Ctrl holds a CR page<br>\* 2: BP_RES_SHARED, current Buffer Ctrl is registered as shared readonly in GCS<br>\* 3: BP_RES_EXCLUSIVE, current Buffer Ctrl is registered as exclusive writable in GCS |
| REF_COUNT      | INTEGER   | Current concurrent access count to the buffer control    |
| PAST_COPY      | INTEGER   | Whether the buffer control is a past copy or has a past copy<br>\* 0: BP_NO_PASTCOPY, not a past copy, default<br>\* 1: BP_IS_PASTCOPY, is a past copy<br>\*   2: BP\_HAS\_PASTCOPY, there has a past copy |
| REMOTE_CR_STATS| INTEGER   | Count of remote requests for CR block                     |
| FLAGS          | RAW(8)    | Buffer control flags                                      |
| BLK_ADDR       | RAW(8)    | Memory address of the block                               |
| CR_SCN         | BIGINT    | SCN of the CR page                                       |
| CR_XID_EXT     | INTEGER   | Extent of the transaction for the CR page                 |
| CR_XID_NODE    | INTEGER   | Node of the transaction for the CR page                   |
| CR_XID_XSN     | INTEGER   | Sequence number of the transaction for the CR page        |
| CR_SSN         | INTEGER   | SSN of the CR page                                       |
| LAST_LFN       | BIGINT    | Redo log sequence number                                   |
| DIRTY_PREV     | RAW(8)    | Previous dirty page                                       |
| DIRTY_NEXT     | RAW(8)    | Next dirty page                                           |
| TRUNC_LFN      | BIGINT    | Redo log sequence number                                   |
| TRUNC_RST      | INTEGER   | HA failure count                                          |
| TRUNC_ASN      | INTEGER   | Archive sequence number                                   |
| TRUNC_BID      | INTEGER   | Redo block number                                         |
| RES_ROLE       | VARCHAR(8)| Resource role of buffer page in the cluster<br>\* LOCAL: Local role<br>\* GLOBAL: Global role |
| WRITE_INST     | INTEGER   | Instance ID currently flushing this page                  |
| OBJ            | BIGINT    | Object ID of the current page                              |
