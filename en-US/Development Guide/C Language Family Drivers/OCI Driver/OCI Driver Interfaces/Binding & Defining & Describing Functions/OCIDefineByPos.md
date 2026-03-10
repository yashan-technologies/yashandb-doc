## Functionality Overview

The OCIDefineByPos function is used to associate items in the select list with types and output data cache.

## Function Declaration

```c
sword OCIDefineByPos ( OCIStmt     *stmtp, 
                       OCIDefine  **defnpp,
                       OCIError    *errhp,
                       ub4          position,
                       void        *valuep,
                       sb4          value_sz,
                       ub2          dty,
                       void        *indp,
                       ub2         *rlenp,
                       ub2         *rcodep,
                       ub4          mode );
```

## Parameter Description

|Parameter Name |Description |
| --------------- | ----------------------------------------------- |
| stmtp (IN/OUT)    | The handle for the requested SQL query operation.   |
| defnpp (IN/OUT)   | A pointer to the define handle.                     |
| errhp (IN/OUT)    | An error handle, which can provide diagnostic information on errors. |
| position (IN)     | The position in the select list.                    |
| valuep (IN/OUT)   | A pointer to the cache or cache array specified by the dty parameter. |
| value_sz (IN)     | The size of each valuep cache (in bytes).          |
| dty (IN)          | Data type.                                          |
| indp (IN)         | A pointer to the indicator variable or array.       |
| rlenp (IN/OUT)    | A pointer to the array for the length of retrieved data. |
| rcodep (OUT)      | A pointer to the array for column-level return codes. |
| mode (IN)         | Supported valid modes.                              |

Currently, some parameters support only the following values:

|Parameter |Valid Values |
| -------- |----------------------------------------------------------------------------------------------------------------------------|
| dty (IN) | SQLT_INT<br />SQLT_STR<br />SQLT_AFC<br />SQLT_CHR<br />SQLT_FLT<br />SQLT_TIMESTAMP<br />SQLT_INTERVAL_DS<br />SQLT_BLOB<br />SQLT_CLOB<br />SQLT_RSET |
| mode     | OCI_DEFAULT<br />OCI_DYNAMIC_FETCH                                                                                                       |
| defnpp   | NULL                                                                                                                       |
| position | \>= 1                                                                                                                      |

Currently, retrieving variable-length types in the output data cache does not support truncation. When retrieving result set data, all variable-length types that encounter insufficient output data cache size will result in an error.