## Functionality Introduction

The OCINlsEnvironmentVariableGet function is used to return the character set ID.

## Function Declaration

```c
sword OCINlsEnvironmentVariableGet ( void       *val,
                                     size_t      size,
                                     ub2         item,
                                     ub2         charset, 
                                     size_t     *rsize );
```

## Parameter Description

|Parameter Name |Description |
| ----------- | ------------------------------------------------------------ |
| val (IN/OUT)   | Character set ID.                                           |
| size (IN)      | Length of the character set ID.                             |
| item (IN)      | Type of character set to retrieve:<br />OCI_NLS_CHARSET_ID: character set.<br />OCI_NLS_NCHARSET_ID: national character set. |
| charset (IN)   | Character set for the configuration name.                   |
| rsize (OUT)    | Length of the character set.                                |

Currently, some parameters only support the following values:

|Parameter |Valid Values |
| ------ |------------------------------------------|
| item | OCI_NLS_CHARSET_ID<br />OCI_NLS_NCHARSET_ID  |
