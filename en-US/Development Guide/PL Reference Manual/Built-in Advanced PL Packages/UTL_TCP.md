The UTL_TCP package provides an additional method for active communication with applications, which includes the CLOSE_ALL_CONNECTIONS procedures and the CRLF variable.

The UTL_TCP package is syntax-compatible only and has no functional impact.

## CRLF

```PLSQL
CRLF CONSTANT VARCHAR(2) 
```

Represents the carriage return and line feed (CRLF) character sequence, used for assignment or initialization.

## CLOSE_ALL_CONNECTIONS

```PLSQL
UTL_TCP.CLOSE_ALL_CONNECTIONS;
```

Closes all connections before PL/SQL program termination to prevent dangling connections. This operation is syntax-compatible only; it returns Succeed by default and has no functional impact.

