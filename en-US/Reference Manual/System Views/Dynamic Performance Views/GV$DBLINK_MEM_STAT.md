This view displays relevant statistics on memory usage for all database links in the sandbox process yex_server.

|Field |Type |Description |
|-------------------|-------------|--|
| GROUP_ID            | NUMBER      | Group ID    |
| GROUP_NODE_ID       | NUMBER      | Node ID within the group |
| INST_ID             | NUMBER      | Instance ID |
| EXT_DRIVER_NAME     | VARCHAR(64) | Driver name |
| EXT_CONNECTION_COUNT | INTEGER     | Number of connections established with remote databases on the driver |
| EXT_CONNECTION_MEMORY | INTEGER    | Memory size occupied by connections on the driver |
| EXT_STATEMENT_MEMORY | INTEGER     | Memory size occupied by statement execution resources on the driver |