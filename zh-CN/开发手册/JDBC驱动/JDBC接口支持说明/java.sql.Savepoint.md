Savepoint用来表示保存点对象，Savepoint可以是命名的，也可以是未命名的。

YashanDB JDBC驱动对Savepoint接口已支持功能：

|  返回类型| 方法| 备注|
|----------------------|--------------------|-----------------------|
| int                  | getSavepointId()   | 只有未命名的保存点可以调用，否则会抛异常。 |
| String               | getSavepointName() | 只有命名的保存点可以调用，否则会抛异常。  |
