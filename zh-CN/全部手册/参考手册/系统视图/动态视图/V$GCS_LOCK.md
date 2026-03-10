本视图显示共享集群全局缓存锁情况。

|  字段| 类型| 描述|
| --- | --- | --- |
| TS# | INTEGER | 表空间 |
| FILE# | INTEGER | 文件号 |
| BLK# | INTEGER | 数据块号 |
| OBJ | BIGINT | 对象ID |
| RES_MODE | VARCHAR(9) | 资源模式<br>* FREE：当前数据块缓存处于空闲状态<br>* SHARE：当前数据块缓存在GCS中登记为共享只读状态<br>* EXCLUSIVE：当前数据块缓存在GCS中登记为独占可写状态 |
| MASTER | TINYINT | 缓存信息中的资源元数据信息所在实例，当缓存无效时为NULL |
| GRC_VERSION | SMALLINT | 缓存信息中的资源元数据信息所在实例对应的GRC版本，当缓存无效时为NULL |
