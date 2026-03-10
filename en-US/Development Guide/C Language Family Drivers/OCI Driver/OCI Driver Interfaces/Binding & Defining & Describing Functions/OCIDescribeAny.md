## Functionality Introduction

The OCIDescribeAny function is used to describe existing schema and sub-schema objects.

## Function Declaration

```c
sword OCIDescribeAny ( OCISvcCtx       *svchp,
                       OCIError        *errhp,
                       void            *objptr,
                       ub4              objptr_len,
                       ub1              objptr_typ,
                       ub1              info_level,
                       ub1              objtyp,
                       OCIDescribe     *dschp );
```

## Parameter Description

|Parameter Name |Description |
| --------------- | ------------------------------------------------------------ |
| svchp (IN)         | Service context handle.                                   |
| errhp (IN/OUT)     | An error handle to retrieve diagnostic information when an error occurs. |
| objptr (IN)        | By passing different values for objptr_typ, objptr can be one of the following:<br/>* A string containing the name of the object to describe. It must be encoded in the character set specified by the charset parameter of the last call to [OCIEnvNlsCreate](../Connection & Authorization & Initialization Functions/OCIEnvNlsCreate).<br/>* A pointer to a REF pointing to the TDO (for types).<br/>* A pointer to the TDO (for types).<br/>This parameter must be non-NULL. |
| objnm_len (IN)    | The length of the name string pointed to by objptr.<br/>If a name is passed, it must be non-zero.<br/>If objptr is a pointer to a TDO or its "REF", it can be zero. |
| objptr_typ (IN)   | The type of the object passed in objptr.                   |
| info_level (IN)   | Reserved for future expansion.<br/>Pass OCI_DEFAULT.      |
| objtyp (IN)       | The type of the schema object being described.             |
| dschp (IN/OUT)    | A describe handle that will be filled with descriptive information about the object after the call.<br/>Must be non-NULL. |

Some parameters only currently support the following values:

|Parameter |Valid Values |
| ---------- |-------------------------------|
| objptr_typ | OCI_OTYPE_NAME                |
| objtyp     | OCI_PTYPE_VIEW<br />OCI_PTYPE_TABLE |
| objptr      | Non-NULL                             |
| objptr_len  | Non-zero                             |
| info_level | 0                             |
