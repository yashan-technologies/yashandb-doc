The Xid interface is a data model that uniquely identifies user-defined XA transactions, including:

|Return Type |Method |Remarks |
|--------|-----------------------------------|--------------------|
| int    | getFormatId()                     | -                   |
| byte[]      | getGlobalTransactionId()            | According to XA specification, this field has a maximum length of 64 bits |
| byte[]      | getBranchQualifier(String value)    | According to XA specification, this field has a maximum length of 64 bits |

The Xid implementation class of the YashanDB JDBC driver is YasXid, which provides the constructor YasXid(int formatId, byte[] gtrid, byte[] bqual) throws XAException.

> **Note**: 
>
> The Xid interface is a standard interface defined by the JDBC specification. Users can use the YasXid implementation provided by the YashanDB JDBC driver or use other implementations (as long as they correctly implement the three methods in the Xid interface).

In YashanDB, the formatId field of Xid will be ignored, and the trailing 0s in the gtrid and bqual fields will be ignored, meaning that {97, 97, 97}, {97, 97, 97, 0}, and {97, 97, 97, 0, 0, 0, 0, 0} are equivalent when used as gtrid or bqual.