This view displays global memory information.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID      | NUMBER    | Group ID                |
| GROUP_NODE_ID | NUMBER    | Node ID within the group |
| INST_ID       | NUMBER    | Instance ID             |
|  NAME   | VARCHAR(32)|  Memory pool name: <br />* data buffer<br />* temporary buffer<br />* large pool<br />* redo buffer<br />* hot cache<br />* share pool <br />* dbwr buffer<br />* job pool<br />* audit queue buffer <br />* global application pool (Obsolete since YashanDB v23.4.1; retained only for backward compatibility)  |
| SIZE   | BIGINT     | Capacity of the memory pool (unit: bytes)  |
