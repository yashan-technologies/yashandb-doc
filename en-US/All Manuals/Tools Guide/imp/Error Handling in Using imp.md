## Import Process Error Handling
During the import process, issues such as existing objects, table constraints, and insufficient system resources may arise, leading to warnings or interruptions in the import operation.

### Row Data Import Errors

If a row cannot be imported normally due to constraints or invalid data, a warning message will be output, and the remaining data for that table will continue to be imported.

Constraint errors include the following:

-  NOT NULL constraint
- Unique constraint
- Primary key constraint
- Referential integrity constraint
- Column constraint

### Object Import Errors

When importing objects, issues such as existing objects may prevent normal object creation. The main scenarios include:

-  Object already exists
- System resource errors

#### Object Already Exists Error

Before importing data, if objects such as tables are not deleted, an "object already exists" error will occur.

- If IGNORE=N, existing objects in the current database will not be replaced, and a "object already exists" warning will be output. Associated data for that object will not be imported.
- If IGNORE=Y, existing objects in the current database will not be replaced, and no warning information will be output. Associated data for that object will be imported normally. If the table already exists, its data, indexes, etc. will be imported normally.

#### System Resource Errors

If the following errors occur, the import operation will be terminated.
|Error Message |Description |
| --------------------------------------------------- | --- |
| *cannot allocate n bytes from x allocator*             | Insufficient memory allocator capacity |
| *no free blocks in large pool*                          | High concurrency leads to insufficient large pool |
| *no free block in x*                                   | Insufficient remaining memory size |
| *no free space in virtual memory pool*                 | Insufficient space in virtual memory pool |

During the import process, if errors other than those listed above occur, only warnings will be output, and the import operation will continue.

## Error Codes

### Database Error Codes

Detailed information about error codes composed of 'YAS-' followed by five digits can be found in the [Database Error Codes](../../Reference Manual/Error Codes).

## imp Error Codes

Error Number: Composed of 'YASIMP-' followed by five digits.

Error Message: Descriptions of error information, where the system communicates specific messages regarding the error through variables in different error scenarios.

### YASIMP-001:EXIM\_ERR\_UNRECOGNIZED\_PARAM

**Message**: unrecognized parameter %s

**Action**: Remove the unrecognized parameter.

### YASIMP-002:EXIM\_ERR\_INVALID\_PARAM\_VALUE

**Message**: invalid value for parameter %s

**Action**: Check the parameter based on the information.

### YASIMP-003:EXIM\_ERR\_INVALID\_PARAM

**Message**: invalid parameter %s occurs

**Action**: Check the data parameter based on the information.

### YASIMP-004:EXIM\_ERR\_PARAMS\_EXPECTED

**Message**: parameter %s is requested

**Action**: Add the necessary parameter based on the information.

### YASIMP-005:EXIM\_ERR\_INVALID\_USERID

**Message**: invalid %s

**Action**: Check the connection string information based on the information.