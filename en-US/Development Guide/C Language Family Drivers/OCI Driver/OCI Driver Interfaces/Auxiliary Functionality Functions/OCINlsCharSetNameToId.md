## Functionality Overview

The OCINlsCharSetNameToId function is used to return the ID corresponding to a character set name.

## Function Declaration

```c
ub2 OCINlsCharSetNameToId ( void            *hndl, 
                            const OraText   *name );
```

## Parameter Description

|Parameter Name |Description |
| ------------ | ---------- |
| hndlp (IN)     | Environment handle. |
| name (IN/OUT)  | Character set name. |

Currently, some parameters only support the following values:

|Parameter |Valid Values |
| ---- | ------------------------ |
| name | GBK<br />UTF8<br />UTF16 |
