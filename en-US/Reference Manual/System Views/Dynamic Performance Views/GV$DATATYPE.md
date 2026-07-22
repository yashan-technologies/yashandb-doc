This view displays all data type information provided by the current system. For a detailed list, refer to the development manual [Data Types](../../../Development Guide/SQL Reference Manual/Data Types/00Data Types), [Cursors](../../../Development Guide/PL Reference Manual/PL Language Fundamentals/Variables/Cursors), and [RECORD](../../../Development Guide/PL Reference Manual/PL Language Fundamentals/Variables/RECORD).
|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID    | NUMBER    | Group ID                             |
| GROUP_NODE_ID | NUMBER  | Node ID within the group             |
| INST_ID     | NUMBER    | Instance ID                          |
| TYPE#       | SMALLINT  | Data type ID                        |
| NAME        | VARCHAR(64) | Data type name                      |
| MAX_SIZE    | INTEGER   | Maximum width corresponding to the data type (in bytes) |