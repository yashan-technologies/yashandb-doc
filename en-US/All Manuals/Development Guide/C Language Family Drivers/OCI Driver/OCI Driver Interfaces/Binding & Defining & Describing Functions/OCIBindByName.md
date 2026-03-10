## Functionality Introduction

The OCIBindByName function is used to bind program variables to placeholders in a SQL statement or PL block. The number of variables should not be less than the number of placeholders in the SQL statement or PL block, and the variable names must match the placeholder names that exist in the SQL statement or PL block.

## Function Declaration

```c
sword OCIBindByName (
              OCIStmt       *stmtp, 
              OCIBind       **bindp,
              OCIError      *errhp,
              const OraText    *placeholder,
              sb4           placeh_len,
              void         *valuep,
              sb4           value_sz,
              ub2           dty,
              void         *indp,
              ub2           *alenp,
              ub2           *rcodep,
              ub4           maxarr_len,
              ub4           *curelep, 
              ub4           mode ); 
```

## Parameter Description

|Parameter Name |Description |
|--------------------|------------------------------------------------------------------------------------------------------------|
| stmtp (IN/OUT)       | The statement handle of the SQL or PL statement being processed.                                               |
| bindpp (IN/OUT)      | The address of the binding handle that is implicitly allocated by this call. The binding handle maintains all binding information for this specific input value. When the statement handle is released, this handle is implicitly released. |
| errhp (IN/OUT)       | An error handle that can be used to obtain diagnostic information when an error occurs.                        |
| placeholder (IN)     | The placeholder attribute specified by name.                                                                    |
| placeh_len (IN)      | The length of the placeholder name.                                                                             |
| valuep (IN/OUT)      | The address of the data value or an array of data values of the type specified by the dty parameter. <br />An array of data values can be specified for mapping to PL tables or providing data for SQL multi-row operations. For LOBs, it must be a pointer to a LOB locator of type OCILobLocator. |
| value_sz (IN)        | The possible maximum size, in bytes, of any data value in this binding variable, passed using valuep. <br />In the case of array binding, this parameter indicates the possible maximum size of any element; the actual size is specified in the alenp parameter. |
| dty (IN)             | The data type of the value to be bound.                                                                         |
| indp (IN/OUT)        | A pointer to the indicator variable or array.                                                                    |
| alenp (IN/OUT)       | A pointer to an array that indicates the actual lengths of the array elements.                                   |
| rcodep (OUT)         | A pointer to an array of column-level return codes. <br />This parameter is ignored for dynamic binding.          |
| maxarr_len (IN)      | The maximum possible number of elements that the user array can hold. <br />Only used for PL indexed table binding. |
| curelep (IN/OUT)     | A pointer to an integer that indicates the actual number of elements in the array before or after the operation is executed. <br />Only used for PL indexed table binding. |
| mode (IN)            | The supported valid modes.                                                                                      |

Some parameters currently only support the following values:

|Parameter |Valid Values |
| ---------- |------------------------------------------------------------------------------------------------------------------------------------------|
| dty (IN)   | SQLT_INT<br />SQLT_STR<br />SQLT_AFC<br />SQLT_LNG<br />SQLT_FLT<br />SQLT_TIMESTAMP<br />SQLT_INTERVAL_DS<br />SQLT_VNU<br />SQLT_BDOUBLE<br />SQLT_BLOB<br />SQLT_CLOB<br />SQLT_RSET |
| rcodep     | NULL                                                                                                                                     |
| *bindpp    | NULL                                                                                                                                     |
| maxarr_len | 0                                                                                                                                        |
| curelep    | NULL                                                                                                                                     |
| mode       | OCI_DEFAULT<br />OCI_DATA_AT_EXEC                                                                                                        |
