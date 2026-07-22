## Functionality Introduction

The OCIBindByPos function is used to bind program variables to placeholders in a SQL statement or PL block, and the number of variables must be no less than the number of placeholders in the SQL statement or PL block. If the number of bound variables exceeds the number of placeholders, the excess will be ignored.

## Function Declaration

```c
sword OCIBindByPos ( OCIStmt       *stmtp, 
                     OCIBind      **bindpp,
                     OCIError      *errhp,
                     ub4            position,
                     void          *valuep,
                     sb4            value_sz,
                     ub2            dty,
                     void          *indp,
                     ub2           *alenp,
                     ub2           *rcodep,
                     ub4            maxarr_len,
                     ub4           *curelep, 
                     ub4            mode );
```

## Parameter Description

|Parameter Name |Description |
| ---------------- | ------------------------------------------------------------ |
| stmtp (IN/OUT)     | The statement handle for the SQL or PL statement being processed. |
| bindpp (IN/OUT)    | The address of the binding handle implicitly allocated by this call. The binding handle maintains all binding information for the specific input value. This handle is implicitly released when the statement handle is released. |
| errhp (IN/OUT)     | An error handle that can be used to obtain diagnostic information when an error occurs. |
| position (IN)      | The placeholder attribute specified by position.              |
| valuep (IN/OUT)    | The address of the data value or an array of data values of the type specified in the dty parameter.<br />An array of data values can be specified for mapping to PL tables or providing data for SQL multi-row operations. For LOBs, this must be a pointer to an OCILobLocator type LOB locator. |
| value_sz (IN)      | The maximum possible specification for any data value in this binding variable, passed using valuep, measured in bytes.<br />In the case of array binding, this parameter represents the maximum possible specification for any element, while the actual specification is indicated by the alenp parameter. |
| dty (IN)           | The data type of the value to be bound.                       |
| indp (IN/OUT)      | A pointer to an indicator variable or array.                  |
| alenp (IN/OUT)     | A pointer to an array that indicates the actual length of the array elements. |
| rcodep (OUT)       | A pointer to an array of column-level return codes.<br />This parameter is ignored for dynamic binding.   |
| maxarr_len (IN)    | The maximum number of elements that the user array can hold.<br />Used only for PL indexed table binding. |
| curelep (IN/OUT)   | The current array length parameter, pointing to a pointer that indicates the actual number of elements in the array before or after the operation is executed.<br />Used only for PL indexed table binding. |
| mode (IN)          | The supported valid modes.                                   |

Some parameters currently support the following values:

|Parameter |Valid Values |
| ---------- |------------------------------------------------------------------------------------------------------------------------------------------|
| dty (IN)   | SQLT_INT<br />SQLT_STR<br />SQLT_AFC<br />SQLT_LNG<br />SQLT_FLT<br />SQLT_TIMESTAMP<br />SQLT_INTERVAL_DS<br />SQLT_VNU<br />SQLT_BDOUBLE<br />SQLT_BLOB<br />SQLT_CLOB<br />SQLT_RSET |
| rcodep     | NULL                                                                                                                                     |
| *bindpp    | NULL                                                                                                                                     |
| maxarr_len | 0                                                                                                                                        |
| curelep    | NULL                                                                                                                                     |
| position   | \>= 1                                                                                                                                    |
| mode       | OCI_DEFAULT<br />OCI_DATA_AT_EXEC                                                                                                        |
