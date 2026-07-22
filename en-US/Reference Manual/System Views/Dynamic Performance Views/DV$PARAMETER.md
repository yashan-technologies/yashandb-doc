This view displays a summary of parameter information for all nodes in an ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP\_ID     | INTEGER       | Group ID                     |
| GROUP\_NODE\_ID | INTEGER       | Node ID within the group     |
| NAME           | VARCHAR(64)   | The name of the parameter    |
| VALUE          | VARCHAR(4096) | The current session's parameter value |
| DEFAULT\_VALUE | VARCHAR(4096) | The default value of the parameter |
| IS\_DEPRECATED | VARCHAR(8)    | Whether the parameter is deprecated |