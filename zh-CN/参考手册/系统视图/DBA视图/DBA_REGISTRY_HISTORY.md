本视图提供有关数据库已执行的升级、安装的历史信息。

|  字段| 类型| 说明|
| --- | --- |-----------------------------------------------------------------------------------------|
| ACTION_TIME | TIMESTAMP(6) | 操作执行的时间戳                                                         |
| ACTION | VARCHAR(30) | 执行的操作类型<br>\*   UPGRADE：数据库版本升级<br>\*   BOOTSTRAP：数据库初始创建                        |
| NAMESPACE | VARCHAR(30) | 操作命名空间，该字段用于对数据库的操作进行分类，指明该操作影响的范围或对象类型。<br>\*   DATAPATCH：表示操作影响的是补丁元数据，当ACTION为BOOTSTRAP时恒为该值<br>\*   SERVER：表示操作影响的是数据库服务端的核心功能 |
| VERSION | VARCHAR(30) | 操作后组件或数据库的版本号                                                             |
| ID  | NUMBER | 唯一标识符                       |
| COMMENTS | VARCHAR(255) | 操作相关的注释信息                                                            |
| BUNDLE_SERIES  | VARCHAR(30) | 补丁包系列信息（兼容字段）                                            |
