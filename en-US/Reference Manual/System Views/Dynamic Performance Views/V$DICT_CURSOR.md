This view displays information about the cursors currently in use.

|Field |Type |Description |
| --- | --- | --- |
| SID | INTEGER | Session ID |
| GLOBAL | VARCHAR(8) | Is it a global cursor?<br>\*   TRUE<br>\*   FALSE |
| SQL\_ID | VARCHAR(13) | SQL ID of the opened cursor<br/>\* -1: Default SQL ID, a cursor that has just been allocated and not yet used<br/>\* -2: Cursor for internally opened system tables<br/>\* -3: Cursor for internally opened user tables<br/>\* other: Cursors opened through SQL, can be joined with V$SQLTEXT to obtain the corresponding SQL statement  |
| TYPE | VARCHAR(32) | Type of the cursor, indicating the type of object the cursor is opened against |
| CONTEXT | VARCHAR(2048) | Context information related to the cursor, reserved field, default is empty |