XAResource接口用于进行XA操作。

YashanDB JDBC驱动对XAResource接口已支持功能：

|  返回类型| 方法| 备注|
|----------|----------------------------------------------|--------------------------------------------------------------------------------------------------------------------------|
| void     | start(Xid xid, int flags) throws XAException | flag支持如下取值：<br/>XAResource.TMNOFLAGS = 0;<br/>XAResource.TMJOIN=2097152;<br/>XAResource.TMRESUME=134217728;              |
| void     | end(Xid xid, int flags) throws XAException   | flag支持如下取值：<br/>XAResource.TMSUSPEND = 33554432;<br/>XAResource.TMSUCCESS = 67108864;<br/>XAResource.TMFAIL = 536870912; |                                                                                                            |
| int      | prepare(Xid xid) throws XAException          |  -                                                                                                                        |
| void     | commit(Xid xid, boolean onePhase)            |      -                                                                                                                    |
| void     | rollback(Xid xid) throws XAException         |  -                                                                                                                        |
| void     | forget(Xid xid) throws XAException           |   -                                                                                                                       |
| Xid[]    | recover(int flag) throws XAException         | 查询当前所有的Xid，参数flag会被忽略                                                                                                   |
| int      | getTransactionTimeout()                      | 获取设置的事务超时时间                                                                                                             |
| boolean  | setTransactionTimeout(int i)                 | 设置事务超时时间（超时机制暂未实现）                                                                                                     |
