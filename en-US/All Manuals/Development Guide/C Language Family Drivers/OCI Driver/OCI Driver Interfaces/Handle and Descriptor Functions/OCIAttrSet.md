## Functionality Overview

The OCIAttrSet function is used to set the attributes of the OCI handle currently pointed to.

## Function Declaration

```c
sword OCIAttrSet ( void        *trgthndlp,
                   ub4          trghndltyp,
                   void        *attributep,
                   ub4          size,
                   ub4          attrtype,
                   OCIError    *errhp );
```

## Parameter Description

|Parameter Name |Description |
| ------------------- | --------------------------------------------- |
| trgthndlp (IN/OUT)  | The handle whose attributes are being modified. |
| trghndltyp (IN/OUT) | The type of the handle for which the attribute is set. |
| attributep (IN)     | A pointer to the attribute value.<br />The attribute value will be copied to the target handle. |
| size (IN)           | The size of the attribute value.                |
| attrtype (IN)       | The type of the attribute to be set.            |
| errhp (IN/OUT)      | An error handle that can be used to obtain diagnostic information when an error occurs. |

The trghndltyp and attrtype parameters currently support the following combinations:

|Valid Values for trghndltyp |Valid Values for attrtype |
| ---------------- |------------------------------------|
| OCI_HTYPE_STMT | OCI_ATTR_PREFETCH_ROWS             |
| OCI_HTYPE_SVCCTX | OCI_ATTR_SERVER                    |
| OCI_HTYPE_SVCCTX | OCI_ATTR_SESSION                   |
| OCI_HTYPE_SVCCTX             | OCI_ATTR_STMTCACHESIZE (reserved parameter, not currently effective) |
| OCI_HTYPE_SESSION | OCI_ATTR_USERNAME                  |
| OCI_HTYPE_SESSION | OCI_ATTR_PASSWORD                  |
| OCI_HTYPE_SESSION | OCI_ATTR_DRIVER_NAME               |
| OCI_HTYPE_SESSION             | OCI_ATTR_CONNECTION_CLASS (reserved parameter, not currently effective) |
| OCI_HTYPE_SESSION             | OCI_ATTR_PURITY (reserved parameter, not currently effective) |