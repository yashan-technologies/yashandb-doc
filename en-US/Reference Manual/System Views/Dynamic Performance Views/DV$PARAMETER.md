This view displays a summary of parameter information for all nodes in a distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID     | INTEGER       | Group ID                     |
| GROUP_NODE_ID | INTEGER       | Node ID within the group     |
| NAME           | VARCHAR(64)   | The name of the parameter    |
| VALUE          | VARCHAR(4096) | The current session's parameter value |
| DEFAULT_VALUE | VARCHAR(4096) | The default value of the parameter |
| IS_DEPRECATED | VARCHAR(8)    | Whether the parameter is deprecated |