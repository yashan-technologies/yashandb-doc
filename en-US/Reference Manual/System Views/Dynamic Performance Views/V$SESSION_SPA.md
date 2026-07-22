This view displays the SPA used by all sessions on the current node.

|Field |Type |Description |
| --- | --- | --- |
|  SID | SMALLINT | Session ID |
| TYPE | VARCHAR(32) | Memory Types <br/>* vm buffer pool: Row execution virtual memory used by the current session, dynamically allocated from the SGA <br/>* large pool: Large object pool used by the current session, dynamically allocated from the SGA <br/>* app pool: Application pool owned by the current session <br/>* pq pool: Parallel query pool used by the current session, dynamically allocated from the MEX pool <br/>* columnar vm buffer pool: Columnar virtual memory buffer owned by the current session  |
| USE_SIZE | BIGINT | Current memory capacity in use (unit: bytes)  |
