The XAConnection interface is used to obtain an XAResource object.

The YashanDB JDBC driver supports the following functionality for the XAConnection interface:

|Return Type |Method |
|----------------------------------|-------------------|
| javax.transaction.xa.XAResource  | getXAResource()   |

> **Note**: 
>
> The XAConnection interface is a subinterface of the PooledConnection interface, so all interfaces in PooledConnection can be used.