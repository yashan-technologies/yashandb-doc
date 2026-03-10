XAConnection接口用于获取XAResource对象。

YashanDB JDBC驱动对XAConnection接口已支持功能：

| 返回类型                             | 方法                |
|----------------------------------|-------------------|
| javax.transaction.xa.XAResource  | getXAResource()   |

> **Note**：
>
> XAConnection接口是PooledConnection接口的子接口，所以PooledConnection中的接口都可以用。
