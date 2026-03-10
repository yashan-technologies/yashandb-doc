This view shows the cursor information for all nodes in the distributed cluster that are currently in use.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID          | INTEGER     | Group ID                                                                                           |
| GROUP_NODE_ID     | INTEGER     | Node ID within the group                                                                           |
| SID               | INTEGER     | Session ID                                                                                         |
| GLOBAL            | VARCHAR(8)  | Whether it is a global cursor<br>\*   TRUE<br>\*   FALSE                                           |
| SQL_ID            | VARCHAR(13) | SQL ID of the cursor that was opened<br>\* -1: Default SQL ID, a cursor just allocated but not yet used<br>\* -2: Cursor opened by system tables internally<br>\* -3: Cursor opened by user tables internally<br>\* other: Cursors opened via SQL, can be queried together with V$SQLTEXT to obtain the corresponding SQL statement |
| TYPE              | VARCHAR(32) | Type of the cursor, indicating the object type the cursor is opened against                       |
| CONTEXT           | VARCHAR(2048)| Context information related to the cursor, reserved for future use, defaults to empty          |