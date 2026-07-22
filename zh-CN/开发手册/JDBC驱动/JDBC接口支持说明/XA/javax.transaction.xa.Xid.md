Xid接口是用户定义XA事务的唯一标识的数据模型，包括：

|  返回类型| 方法| 备注|
|--------|-----------------------------------|--------------------|
| int    | getFormatId()                     | -                   |
| byte[] | getGlobalTransactionId()          | 按照XA规范，该字段长度最大为64位 |
| byte[] | getBranchQualifier(String value)  | 按照XA规范，该字段长度最大为64位 |

YashanDB JDBC驱动的Xid实现类为YasXid，提供YasXid(int formatId, byte[] gtrid, byte[] bqual) throws XAException构造方法。

> **Note**: 
>
> Xid接口是JDBC规范定义的标准接口，用户可以使用YashanDB JDBC驱动提供的YasXid实现，也可以使用其他实现（只需正确实现了Xid接口中的三个方法即可）。

在YashanDB中，Xid的formatId字段会被忽略，gtrid和bqual字段尾部的0会被忽略，即{97, 97, 97}、{97, 97, 97, 0}与{97, 97, 97, 0, 0, 0, 0, 0}在作为gtrid或bqual时是等价的。
