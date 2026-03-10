The XAResource interface is used for XA operations.

The YashanDB JDBC driver supports the XAResource interface functionality:

|Return Type |Method |Remarks |
|----------|----------------------------------------------|--------------------------------------------------------------------------------------------------------------------------|
| void        | start(Xid xid, int flags) throws XAException | The flag supports the following values:<br/>XAResource.TMNOFLAGS = 0;<br/>XAResource.TMJOIN=2097152;<br/>XAResource.TMRESUME=134217728; |
| void        | end(Xid xid, int flags) throws XAException   | The flag supports the following values:<br/>XAResource.TMSUSPEND = 33554432;<br/>XAResource.TMSUCCESS = 67108864;<br/>XAResource.TMFAIL = 536870912; |
| int      | prepare(Xid xid) throws XAException          |  -                                                                                                                        |
| void     | commit(Xid xid, boolean onePhase)            |      -                                                                                                                    |
| void     | rollback(Xid xid) throws XAException         |  -                                                                                                                        |
| void     | forget(Xid xid) throws XAException           |   -                                                                                                                       |
| Xid[]       | recover(int flag) throws XAException         | Queries all current Xid; the parameter flag will be ignored                                                             |
| int         | getTransactionTimeout()                      | Gets the configured transaction timeout                                                                                 |
| boolean     | setTransactionTimeout(int i)                 | Sets the transaction timeout (timeout mechanism is not yet implemented)                                                  |