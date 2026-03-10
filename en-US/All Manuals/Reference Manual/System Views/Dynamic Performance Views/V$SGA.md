This view displays SGA information.

|Field |Type |Description |
| --- | --- | --- |
|  NAME   | VARCHAR(32)|  Memory pool name: <br />* data buffer<br />* temporary buffer<br />* large pool<br />* redo buffer<br />* hot cache<br />* share pool <br />* dbwr buffer<br />* job pool<br />* audit queue buffer <br />* global application pool (Obsolete since YashanDB v23.4.1; retained only for backward compatibility)  |
| SIZE   | BIGINT     | Capacity of the memory pool (unit: bytes)  |
